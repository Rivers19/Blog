[toc]

# 1.Bot-Skill平台模型概述

Bot-Skill平台中已经被大家耳熟能详的诸如意图、实体和槽位等概念已经非常普及了，我个人将目前Bot归纳为六个基本要素，这六大要素形成一个基本的逻辑链路，用于解决在接受到一个用户Query后的“触发-解析（完整）-执行-返回”这四个基本问题。（这里Query指的是经过ASR和语音转文本后的Query）



这六个基本要素如下：

**·Intent（意图）**

**·Pattern（句式模板）**

**·Entity（实体）**

**·Slot（槽位）**

**·Response（回复）**

**·Context（上下文）**



其中前五个要素（除去用于解决多轮对话的Context），分布在该链路中的不同位置，解决Query从进来到返回结果的子问题。

![img](https://images-cdn.shimo.im/PckXAyvBdbwnuVxL/%E5%9B%BE1.png!thumbnail)

*图-1（Bot逻辑链路图）*





**1）意图分类/排序**

Query文本经过分词后，首先会将该Query进行意图分类或排序，以找到最最有可能的Intent（目前大多以排序模型为主，从中挑选一个权重最高的Intent）。该模块主要涉及的要素是Intent与Pattern，通过Pattern的模板/文法配置与对话样本的模型训练两种方式，让系统更加智能触发对应Intent。

**2）槽位完整填充**

触发某个意图后下一步要解决的问题，就是将实体填充到对应的槽位。同时要解决的一个子问题就是对于请求后续服务来讲必填的槽位要保证被填充上，这个模块主要涉及的要素为Slot和Entity。

**3）意图回复**

意图回复针对触发的意图进行回复，也即Response要素。



上面的五个要素的共同作用下，会完成一次对话闭环，当然还有关于Context多轮设计的内容，后面也会以一个模块单独去讲。



# 2.Bot-Skill模块化分析

## 2.1 触发的智能：Intent与Pattern

之所以叫“触发的智能”，因为这可能是最能体现AI的地方之一（也可能没有之一....）。将所有相同含义的话抽象为一个Intent在以前不能做到，就是因为不可能去枚举所有的可能话术，而机器/深度学习的模型的特性，可以通过部分的对话样本训练，实现大量的程度可控的泛化。

关于Bot-Skill中的意图和句式模板，各个平台都基本类似，先是自建意图，在意图中输入可以触发此意图的句式模板，为了方便配置，也可以直接引用意图中的槽位，来直接覆盖槽位中所含的内容。

但在Pattern模块还有个经常被忽略的维度：

**偏工程构造的句式模板与文法设计，在实际开发一个语音Skill时，即使能够在句式中引用槽位来批量覆盖词汇，但这种抽象程度远远不够。可能还需要枚举大量的“同特征词汇”，可能因为槽位或词汇之间顺序调整就要多造N多个句式，又或者该意图中均为“非必填槽”，还需要枚举各种“有无”情况，都会造成大量繁琐操作。**

### 2.1.1 模板与文法设计理念

**关于偏工程特征的模板与文法的设计**，很多平台都没有体现出来。它主要解决在配置意图句式中，可以通过简单的功能操作来抽象覆盖大量句式。

在模板符号与文法设计上每家平台都会有所差别，但我个人用三个关键来概括其核心，他们分别是：

- **抽象**

- **有无**

- **顺序**

下面我以对外公开的百度Unit来简单说明一下其设计逻辑。

#### 2.1.1.1 抽象

槽位中的实体，往往是对一个领域内词汇的抽象集合（比如@China-city中会含有所有中国城市名称），在构造句式时引用这个实体，在句式中的该片段只要出现该实体中所含有的词汇就全部会被识别。如果该实体含有1000个值，那么意味着只配置一个句式等于枚举了1000个。



但除槽位/实体之外的词汇，在真实场景中还存在大量的类似具有相同含义，但不同描述的词汇或短语表达。比如一个订火车票的场景，句式前面可能会出现类似“我需要、我想要、我要订、帮我订、订一下、帮我找找....”的描述，如果一个个枚举可以想象工作量之大。**但本质上他们可以被解读抽象为一个“查询特征词”集合，所以可以针对这类词汇构建一个模板（命名为：Booking），将类似词语添加进去。句式中只要配置一个模板，就可以承接住用户的不同问法。**



它的另一个好处就是这些“特征词模板”也可以积累管理，如果下次要做一个订飞机票、订船票、订酒店的Skill，都可以**直接引用相同的【booking】模板**。例如：

![img](https://images-cdn.shimo.im/tdLuELnIbsQoDHtX/%E5%B1%8F%E5%B9%95%E5%BF%AB%E7%85%A7_2018_09_10_%E4%B8%8B%E5%8D%889.59.10.png!thumbnail)



#### 2.1.1.2 有无

在解决抽象的问题后，句式的触发还有两个维度非常重要，一个是“有无”，也就是句式中的某个要素对于该意图触发来讲的必要性。**槽位的“必填性”是一直被强调的概念，但对于句式触发的“必要性”却一直被忽略**。对于意图触发来讲，槽位反而不一定重要。（关于这个逻辑如果大家有兴趣，可以看我之前写的一篇关于“意图必要要素理论”的文章）。

比如订火车票这个场景来说，三个槽位“From-city”、“To-city”和“Date-time”均为必填，但对于意图的触发来讲都是非必要的，反而是“我要订、我想订”以及“火车票”这些词汇（非槽位/实体）构成的“我要订火车票”对于意图的触发是必要条件。

**了解意图触发“必要性”后，模板中关于“必要性”的设计，就是通过对于一个句式不同要素的“必要性”的可配置，来进一步对句式进行抽象。**如果一个意图的触发句式中包含5个模板/槽位，分别为【A】【B】【C】【D】【E】，在不考虑顺序的情况下，假设只有【A】对于意图触发是必要的，其余四个均为非必要。那枚举式的配置下我们需要配置（C4x3x2x1）也即24种句式模板（注意这还是在不考虑顺序维度的情况下）

![img](https://images-cdn.shimo.im/ya3ei4SgJYsf2UqR/%E5%9B%BE2.png!thumbnail)

*图-2“有无”维度可配置*

如果我们可以针对这五个要素，从对于句式触发的“必要性”上进行配置，也即设定【A】为必须存在，其余四个要素则均为“非必要”，就可以将24种句式模板抽象为只有一种。

#### 2.1.1.3顺序

**关于句式触发意图的另一个维度为“顺序”。有时句式模板中的多个要素顺序不同，也会影响意图的触发。**比如对于查询天气来讲，这六类要素相同但顺序不同的句式，说出来应该是都可以被触发的：：

- *【city】【date】天气*

- *【date】【city】天气*

- *【city】天气【date】*

- *【date】天气【city】*

- *天气【city】【date】*

- *天气【date】【city】*

如果可以针对句式中的要素，从另一个维度即“出现的顺序对于句式触发”实现可配置化，又可以对句式做进一步的抽象。

Unit对这个在这个维度的设计逻辑，我个人认为很好地解决了这个问题。**它可以对句式模板中的每个要素的顺序进行顺序配置如“1、2、3”，就代表3个要素只能顺序出现才能被意图触发；其次如果你将多个要素的序号设定为相同，则他们之间的顺序可以完全任意出现，但都能被意图接住；最后如果某个要素的序号为“0”，则可以出现在句式中的任意位置。依照这样的设计逻辑，上面6类需要枚举（在不考虑要素有无对于触发必要性的前提下），就又可以抽象为一种配置，如下图：**



![img](https://images-cdn.shimo.im/9l0N083AqCYnFDcI/%E5%9B%BE3.png!thumbnail)*图-3“顺序”维度可配置*

### 2.1.2 模板与文法设计优劣

上面谈了基于特征工程的模板与文法设计的好处，但任何硬币都有两面，它也有很大的弊端。

**（1）优势与好处**

- 让开发者从意图句式/模板的繁琐配置中解脱出来，极大程度抽象句式的配置。

- 模板本身具有“可叠加性”，可以随着时间积累和管理。

- 对于一开始没有什么对话样本数据去训练模型的窘境，可以通过模板与文法在短期内承接用户大量句式

**（2）弊端**

模板与文法过于依赖特征工程，而且它的本质与AI理念相悖。**它的工程理念还是基于对数量枚举的抽象，以及从“有无和顺序”这种逻辑维度的可配置性出发解决问题，但 通过模板/文法的句式最大弊端在于它没有办法通过模型去训练从而持续优化NLU模型。这一点在Dialogflow上有特别的说明，强调希望开发者去通过纯碎的对话文本去配置，以此可以训练模型从而持续优化. **

总结下来，基于模板和文法的句式配置效果“短平快”，但长期下来无法实现可闭环的持续对算法模型的优化；而基于纯碎对话文本则需要大量的数据和标注，是一种耗时耗力，但长期积累下来才能有效果的路线。

## 2.2 “三位一体”解析：Slot-Entity-Value

### 2.2.1 实体填充槽位逻辑

触发意图后，接下来要解决的问题，如何在Query中抽取关键参数。现在比较熟知的概念“槽位、实体和词典值”是专门用于解决这个问题，主要说下着三个要素是构建了怎么样的逻辑模型，来解决这个问题。

![img](https://images-cdn.shimo.im/6MoJH2JSqNg0Mwza/%E5%B1%8F%E5%B9%95%E5%BF%AB%E7%85%A7_2018_09_14_%E4%B8%8A%E5%8D%8810.24.08.png!thumbnail)

​                                                *图-4“槽位-实体-值”逻辑*



**如本模块的小标题，个人习惯将这三个要素Entity-Slot-Value定义为“三位一体”的关系。实体是为了专门承接住用户Query中的关键参数。实体的来源以及其中内涵的具象值则是根据业务场景不同而定。实体中会内含一些该实体的特定“值”，这些值在分词过后会被单独标记出来；与此同时必须创建一个槽位，该槽位必须关联该特定的实体。注意接下来完成“实体填充”这个动作的逻辑条件：**



**当且仅当该特定实体1中的某一个值1-1出现，并且刚好对应上意图内关联该特定实体1的槽位1的条件成立，那么值“1-1”会插入到特定的槽位1中（否则就乱了），导致“实体填槽”这个动作的完成，也即获得“关键参数”这个结果。**

### 2.2.2 必要与必填

关于槽位的必填功能本身就不多说了，重点在于为什么会有“必填槽”的设定，**“必填槽”的设定本身，是为了解决一个限定条件下的问题，这个限定条件刚好连接我在Pattern模块所说的“意图触发必要”逻辑。**

**一个关联实体的槽位本质上就是一堆领域词汇的集合，****这个槽位****（所关联实体的内涵词汇集合）****可能对于意图的触发来讲是“非必要”的，但对于意图的Action来讲却是必填的，所以才会形成必填槽及其追槽方式的设定。****因为对于意图触发“非必要”意味着该槽位可以不出现，而对于意图Action的必填则意味着请求服务必须使用到该槽位的值，那么就需要一个功能去填补这个特殊情况下，把槽位补上的功能。**

如果不符合这个限定条件，就不会出现“必填槽”的概念。为了进一步说明，可以通过“必要”和“必填”的二维视角解读：

|        | 必填        | 非必填        |
| ------ | ----------- | ------------- |
| 必要   | 必要-必填   | 必要-非必填   |
| 非必要 | 非必要-必填 | 非必要-非必填 |

首先可以明确的是，对于最后两个“非必填”的维度是不需要理会的，因为他们本身就是“非必填”，如果所有槽位本身就是“非必填”就不需要“必填槽”的概念，所以先将他们这两种情况排除掉，他们肯定不是“必填槽”出现的原因。

其次可以再看“非必要-必填”，这个也就是我刚才提到的“限定条件”，必填槽的功能逻辑就是为了解决这个单一情况而出现的。

重点是在“必要-必填”这种情况。**如一个意图中含有两个槽位【A】【B】，他们两个对于意图Action来讲，里面的值都是必须要含有的（必填）；同时对于意图的触发来讲，【A】【B】槽位中的值也必须同时出现，也就是说他们两同时存在是该意图触发的“充要条件”。只要该意图被触发，【A】与【B】槽位中的值是已经必然填充上的，那这种情况是不需要“必填槽”的设定以及后续的补槽功能，因为“充要条件”是可以因果互推的，意图触发则可以反推出【A】【B】已经必然被填充上。**

举一个具象的例子，一个“查询人物的某种属性”的意图，【人物名称】与【人物属性】两个槽位只有同时出现，这个意图才能被触发：

- *【@人物名称】的【@人物属性】是什么？*

当该意图被触发的时候，就意味着执行意图的两个必须有的槽位值已经被填充上，无需必填槽的设定及追问等方式。

通过上面两节，大概说明了在“Slot-Entity-Value”这个模块是如何解决获取关键参数这个问题的逻辑，与之对应功能上的体现，有创建/引用实体；在槽位列表中，也需要给被创建的槽位“引用特定实体”的功能。

对“槽位列表”为什么会有“必填槽及其补槽方式”的功能的原因，也做出了比较合理的解释。

![img](https://images-cdn.shimo.im/vrKfVKO67Dw4xdBE/%E5%9B%BE5.png!thumbnail)

*图-5思必驰DUI-槽位列表*



## 2.3 多模态-回复输出：Response



在Bot回复的设计上，可以笼统分为“**基础回复**”与“**多模回复**”两类形式。基础回复包含“**可引用槽位动态文本回复**”与“**基于Webhoolk服务**”的回复。这两种回复形式在DuerOS或小米小爱平台上都可以直接体验，在这里就不作为重点去说了。

主谈一下多模态形式回复，现在随着有屏设备的普及，只有文本TTS的回复在平台上越来越无法满足多模态回复产品需求。这方面做得比较有特点是“**海知智能**”和“**思必驰DUI**”。

### 2.3.1 海知--素材管理库

海知平台上有一个素材管理库，有点类似于“微信公众号”中的素材管理，开发者只有先在自己APP的素材管理库中上传不同形式（图片、音频、视频）素材后，才能在自定义技能的回复模块中使用。



![img](https://images-cdn.shimo.im/DhZ6UGX86UMR9EYX/%E5%9B%BE6.png!thumbnail)

*图-6海知智能素材管理库*



### 2.3.2 思必驰DUI--控件与逻辑

思必驰DUI回复中有两点设计比较有特色，其一是可以针对意图Action请求结果的不同逻辑，进行个性化配置，每个逻辑下的回复配置都是独立的（最典型的比如“找到结果”和“没有找到结果”）；其二是单独针对“有屏设备”支持丰富的可配置控件。个人感觉，丰富的回复逻辑与控件是DUI平台的优势，但也同时成为了他们的劣势。因为“回复”仅作为Bot中的一个模块，占据了大量空间显得过于臃肿，初来的开发者很难一下搞懂如何配置。

![img](https://images-cdn.shimo.im/u60Q3V6Uai03YxF9/%E5%B1%8F%E5%B9%95%E5%BF%AB%E7%85%A7_2018_09_14_%E4%B8%8A%E5%8D%8810.29.56.png!thumbnail)

*图-7思必驰DUI回复控件*

## 2.4 基于Context的多轮对话设计

关于多轮对话这块，首先说一下目前多轮对话的大体情况。依据底层实现的不同，多轮对话可以分为“闲聊型多轮”和“任务型多轮”，其中“闲聊型多轮”是基于记忆神经网络模型的，以小冰为代表产品，而“任务型多轮”是现在大多数平台基于逻辑和规则去实现的多轮对话，也是产品经理可以更多介入、进行产品设计的模块。

**目前比较主流的方式（也是我个人比较推崇和参考进行设计的）是以Dialogflow和DuerOS的基于Context（上下文）的多轮对话，其次国内还有小米小爱和猎户OS基于这个底层模式进行了一些快捷方式和简化的“前置意图设计”。这一节以Context的设计为主，以前置意图设计为辅来谈谈对任务型多轮对话的理解。**

### 2.4.1 多轮问题本质及分类

**（1）问题本质**

核心问题会有两个：

其一，该不该接？（本质上，这是一个条件判定问题）

其二，怎么接？（如何把上下文组成完整信息）

**（2）多轮问题分类**

多轮对话大致可以进行如下类型

- **线性多轮**

旨在收集足够的语义槽并获取用户的关键信息后，执行对应的Action逻辑

- **非线性多轮**

非线性多轮又按照是否“跨意图”分为以下三个类型：

- **非线性意图内多轮对话：意图不变，槽位的更改或新增**

- **非线性跨意图对话圈内意图切换：跨意图，同层级之间**

- **非线性跨意图路径分支多轮对话：跨意图，不同层级之间形成依赖路径分支，且不同分支之间意图不能随意切换**



### 2.4.2 Context概念及逻辑关系

Context的两个核心作用也即“条件判定”和“承载/传递信息”刚好对应上面任务型多轮对话要解决的两个本质问题。

#### 2.4.2.1 Input / Output context 概念及作用

**（1）Output context** 

- **定义**：每个Intent都可以在其后输出一个context，即output context。

- **生命周期**：每个被输出的Context都有生命周期的存在，即只有在其有效期内，context的两大作用才会生效。

- **承载/传递信息**：被输出的output context将会承载该Intent的槽位/值，以在另一个意图触发后，将所需要的关键信息传递过去，组合成完整的intent。

**（2）Iutput context**

- **定义**：某些意图，只有需要在特定上文（意图）的情况下才会被触发，则此意图需要配置对应的Input context。

- **条件判定**
    - 在存在条件判定功能条件下，只有当上一轮的intent1的output context与某个intent2的input context符合触发条件才会触发Intent2（不过“触发条件”后面会提到）。

- **接受/共享槽位信息**：将上一轮意图的槽位/值传递至本轮触发的意图，组合成完整的intent以再一次请求服务。

#### 2.4.2.2 input / Output context 关系及工作方式

**（1）Input之间关系**

- “或”关系

在小米/猎户OS代表的“前置意图设计”中，每个意图都有一个代表自己的输出，那就意味着在该Skill种所有意图本质上都是平级关系。一个意图，如果需要允许多个意图都可以触发或传递槽位信息，那么其input context之间的关系必然是“或”。

- “且”关系

在以Dialogflow为代表的Context设计中，允许自由配置多输入/多输出是其一，其二再加上“依赖树”对话结构，是“且”的关系。

（2）基本逻辑方式

当且仅当上一轮被触发的Intent1的Output context与本轮某个已经配置好的Intent2的Input context符合触发条件时，该Intent2会通过条件判定机制被触发，同时从上一轮的Intent1中继承全部槽位/值，对本轮出现的冲突类型槽位值进行更新与替换，组合成完整的新意图。

（3）Output可刷新性

Output的可刷新性体现在，如果某个意图的Input和Output中配置了相同的“ContextA”，那么ContextA的生命周期和其中所承载的值会被重置更新。

**这一特性对于2轮以上的意图内多轮非常重要，因为ContextA中的承载槽位/值无法随着对话轮数去更新某个槽位的值，就不符合对话中的“信息就近原则”**（具体的体现再下面的Case中会体现出来）。



### 2.4.3 基于Context的意图内槽位更新/替换

**（1）问题抽象描述**

意图本身不变，但用户对已经触发的意图中某些已填槽位值进行修改；或是在该意图中增加槽位，其余已填写的槽位全部继承。下面举一个比较典型的Case来说明解这类问题的逻辑。

**（2）设计解**

以查天气为例，假设用户第一轮已经完整填充关于【city】和【date】的槽位，第二轮需要更新已填充的槽位值：

*Q1：北京今天天气*

*Q2；那南京呢   /那明天呢*

那么基于Context的配置方法如下，需要单独配置一个Intent以承接所有意图内的槽位更新

| 意图配置         | 典型句式          | input context | output context |
| ---------------- | ----------------- | ------------- | -------------- |
| 查天气           | 北京今天天气      |               | A              |
| 查天气意图内多轮 | 那南京呢/那明天呢 | A             | A              |



- **条件判定**

如果直接问“那{date}呢、那{city}呢”是必然不会触发意图的，因为对话管理中并没有“A”存在。



- **槽位引用默认继承**

**触发意图后，槽位列表中会默认将A中的所有槽位/值全部继承，将冲突的槽位值进行更新，以形成新的意图触发。**在这里如果Q2问“那南京呢”，会将A中的【city】由“北京”替换为“南京”，组合成一个新的Intent去继续调取天气服务。

- **槽位值刷新机制**

输出的Output context“A”中所承载的值会被刷新为最新的“值”，以保证意图内多轮对话的“信息就近原则”。比如在上面的case中，假设Q2问到得是“那南京呢”，ContextA中的【city】会由“北京”更新为“南京”保留下来，也就是前面提到的“可刷新性”的作用。

**从逻辑上推演就可知道，如果不存在Context中的槽位/值刷****不存在刷****新机制，那么ContextA中的【city】值会仍然保留为“北京”，这时候如果你在Q3轮问到“那明天呢”，返回的结果将会是“北京明天”的天气情况。但问题在于用户Q2轮已经问得是“南京”这个城市，它的Q3的明天的意思根据对话的“信息就近原则”应该是查询“南京天气”更为合理，解决这个问题的方式就是Context中的承载信息必须具有可刷新性**



### 2.4.4 基于Context的跨意图槽位值继承

**（1）问题**

**某个Skill由n个意图构成，意图之间的槽位完全相同可共享；三个意图作为首轮意图在用户Query中有槽位的情况下均可被直接触发，然后再继续的对话中无需再说槽位，可以实现跨意图的槽位继承。**

比如“查天气”、“查限行尾号”和“查空气质量”分别为三个不同的Intent（假设），他们之间的在Q1触发并填充完必填槽【city】和【date】之后，Q2轮之后再问“那限行尾号”、“那空气质量”呢这样的case可以直接给出结果，而不需要再继续追问用户以填充这两个槽位。

*Q1：北京今天天气*

*Q2：那限行尾号呢*

*Q3：那空气质量呢*

**（2）设计解**

**在基于Context的设计中，可以通过下面的配置方式去解这类Case。其中无论首轮触发的是哪个Intent,都必然会收集到两个必填槽【city】与【date】，那么三个intent的output context均可设定为“A”，其中承载的也是上面两个槽位/值**。

| Intent       | pattern          | slot         | input | output |
| ------------ | ---------------- | ------------ | ----- | ------ |
| 查天气       | 北京今天天气     | city、date   |       | A      |
| 查空气质量   | 北京今天空气质量 | city、date   |       | A      |
| 查限行尾号   | 北京今天限行尾号 | city、date   |       | A      |
| 查天气-1     | 那天气呢         | #A.city/date | A     | A      |
| 查空气质量-1 | 那空气质量呢     | #A.city/date | A     | A      |
| 查限行尾号-1 | 那限行尾号呢     | #Acity/date  | A     | A      |

**对于跨意图（同层级）的槽位共享继承这类Case，他们为什么有必要去实现跨意图，根本原因是因为他们有可以共享继承的某个Slot。那么这有N个Intent是哪一个被触发执行（填好槽），都意味着我们收集到了用户某个槽位中的信息，那也就意味着无论下一轮是哪个被触发实际上我们并不Care，只要它需要引用继承这个槽位信息，就都需要在Input中配置这个Context。**

所以在配置方法上可以看到，三个不同的意图配置的Output context是完全一致的（代表他们内涵同样的槽位/值），三个负责承接这类case的多轮意图，也自然都配置A。同时每个意图都Output context也不意味着代表它自个，形象点说它更像是给对话状态“续命和更新”

### 2.4.5 前置意图设计

说完了基于Context的多轮对话，接下来看看小米和猎户OS的“前置意图设计”，其实这个前置意图的底层逻辑，同样是基于Context，只不过两家平台各自针对自己的理解，做了一些具象化的改变让配置更加简便易懂，但同时也不可避免地造成了一些弊端。

#### 2.4.5.1 前置意图设计概述

前置意图设计中分为“输出意图”和“前置意图”。**“输出意图”也即上面的Output context，它的作用也是相同的可以承载该意图中的槽位/值，唯一的不同是在创建每个意图时，都由系统默认配置一个“代表自己”的输出意图（开发者无法对输出进行配置），也就是说每个意图都有一个代表自己的“符号”并承载自己的槽位/值（输出意图）。**

那么这个设定也就必然导致了前置意图的关系必须为“或”而不能是且，因为在跨意图的多轮对话配置上，一定会出现某个意图允许多个其他意图触发的情况，而多个其他意图又已经由系统默认设定好属于自己的“输出意图”，那么该意图的“前置意图”必须允许配置多个，同时他们之间的关系只能是“或”，才能保证多个意图之后都允许触发它。这样的前置意图设计逻辑有优点也有其缺陷，下面会以同样的两类Case去进行验证。

#### 2.4.5.2 意图内槽位值更新/替换与“输出前置意图”开关

在猎户OS的多轮配置中，会有一个非常明显的“输出前置意图开关”，这个开关就是为了单独解决“意图内槽位更新/替换”这个Case的。上面已经知道系统会默认帮每个意图都建立一个属于自己的输出意图

![img](https://images-cdn.shimo.im/rJvmrnN2X1Qs77LD/%E5%B1%8F%E5%B9%95%E5%BF%AB%E7%85%A7_2018_09_14_%E4%B8%8A%E5%8D%8810.33.12.png!thumbnail)

*图示-9猎户OS前置意图开关*

如果对“输出前置意图开关”打开后，则这个意图被触发执行后，其“输出意图”输出的就不再是“代表自己这个意图的输出意图”，而是更新其前置意图的“槽位/值”和生效轮数，输出的意图也是自己配置的“前置意图”

**这种方式与Context配置中两个均为“A/A”去解决该类问题的本质是一样的，均是更新“Context A”的生效轮数和其中承载的值，只不过在方法上不再是自己配，而是通过系统的开关设定去解决，配置比较简单。**

#### 2.4.5.3 跨意图槽位继承--“前置意图”弊端

**任何一个intent都有一个代表自己的“输出意图”，如果你想要一个意图可以继承来自多个意图的槽位/值，那么在前置意图的关系则必然是“或”（且肯定要采取多个配置）。**仍然是针对“查天气”“查空气质量”和“查限行尾号”这三个跨意图之间的多轮对话来解，配置的方法呈现如下

| Intent       | pattern          | slot         | input     | output |
| ------------ | ---------------- | ------------ | --------- | ------ |
| 查天气       | 北京今天天气     | city、date   |           | A      |
| 查空气质量   | 北京今天空气质量 | city、date   |           | B      |
| 查限行尾号   | 北京今天限行尾号 | city、date   |           | C      |
| 查天气-1     | 那天气呢         | #A.city/date | B/C/E/F/D | D      |
| 查空气质量-1 | 那空气质量呢     | #A.city/date | A/C/D/F/E | E      |
| 查限行尾号-1 | 那限行尾号呢     | #Acity/date  | A/B/D/E/F | F      |



通过配置结果，可以清晰对比出“猎户/小米”的前置意图设计和Context对这类问题的解不同，前置意图设计中每个Intent有一个代表自己的Context，**同时****每个意图自然是可以被其他多个意图都能触发，所以他们配置的Input context（前置意图）会繁琐臃肿，但根本原因并不是因为这个三个意图之后可以接某个意图，而是因为这三个意图之间刚好有可以共享继承的槽位。前置意图设计最大的问题就是配置起来显得很臃肿，如果场景简单可能还好，如果是非常复杂的场景（意图很多），那么配置和维护的成本会非常高。

当然前置意图设计也并非完全没有优势，虽然在操作上比较繁琐需要枚举，但其底层设计思想将“每个意图配置一个代表自己的输出意图，前置意图需要什么就枚举式配什么”，非常符合人的直觉思维，在这一点上我也深有体会.....绝大多数开发者对于这样的设计能够秒懂。

以上是对于Context和前置意图多轮对话的理解，篇幅原因只能把核心思想和比较简单的case拿出来作说明，如果之后大家还有兴趣的话，可以单独把多轮对话这块拎出来系统性谈谈，关于任务型的多轮对话就先说到这里了。

## 2.5 Bot-Skill整体结构与方法论

回顾最开始的主题，Bot六大基本构成要素“Intent、Pattern、Entity、Slot、Response和Context”，在整个对话过程中以怎样的模块化分布和逻辑关系去解不同子问题，已经贪玩。理解了这些要素之间的关系后，Bot操作方法论以及平台所需要支持的功能，自然是在这个底层逻辑模型下更具象层面的自然涌现。

而对于这六种要素的构成整个Bot交互模型结构，大家可以参考下面的图示，基本是各家平台的Bot交互模型都类似。

![img](https://images-cdn.shimo.im/JpvOEtVTnNgJHKz4/%E5%9B%BE10.png!thumbnail)

*图10-整体交互模型*



# 3.Bot-Skill平台的现状与演化

## 3.1 Bot平台现状与演化方向

目前国内外的Bot平台综合来看能力和逼格有高有低，但基本逻辑都差不太多。这也是目前对话平台的天花板，即使增加一些功能性的东西，也很难有非线性增长突破。对于如何突破天花板地演化方向，我个人有如下三个层面的看法：

### （1）功能层面

- 可视化-简易性配置

现在的Bot-Skill平台虽然可以配置出一套完整的语音技能，但意图与意图之间以列表形式平铺，加上用于承接多轮的意图后，整个Skill的配置和后期维护成本较高。其二，当前单个Bot-Skill的整体逻辑没办法进行宏观层面上的配置和呈现。

所以以可视化（类似于流程图）的配置形式，对每个Skill的整个意图逻辑链路，能有完整清晰的呈现，通过在画布中以“拖拽”式地交互完成Bot技能的创建，是我觉得每一个平台都可能要考虑的一个方向。

### （2）多维度层面

- 多模态

几乎当前所有的Bot平台，都是将Query作为输入，以意图作为触发节点的方式承接信息。既然是一种信息的输入，就不单单只有语音/语义，触发的节点也并非一定是“意图”，“意图”只是基于Bot框架逻辑的一种“触发节点”的具体表现，但它本身也是“触发节点”抽象集合的一个子集。

图像、视频、事件和体感（如车载中的传感器），也都是信息，如何收集到这些信息作为输入的触发节点，并且与当前的Bot底层框架深度结合起来，让平台不仅仅存在对话逻辑这一个维度，而是可以实现多维度多模态的输入与输出，是平台实现“降维打击”的一种方式。

### （3）垂直领域的演化

- 特性演化

目前业内大多数平台都定位于偏“通用”的性质，在平台A上可以做到得技能，在平台B也可以完成（虽然多少有一些差异，但并不足够明显），这也是目前市面上语音交互Skill同质化比较严重的原因之一。

在偏通用性质的平台上，基于平台和公司的基因，根据自身公司的独特业务和场景的需求，演化出更具有“垂直领域特色”的Bot-Skill平台是一种很大的可能。特别是针对AI领域的创业公司来说，这更可能会成为一个突围地机会。毕竟大厂们更偏向于适用领域广泛，通用性强的平台。而对于创业公司来说每家的具体业务和场景的侧重点都有所不同，反而更有可能演化出具有自家特色的平台设计。

这个逻辑也类似于物种的演化，比如同样是“鹿”（类比为Bot通用平台），长颈鹿演化出的长脖子，可以帮助其获取更高树木的食物，而角鹿演化出的大角，用来吸引异性，各自有各自的特色和演化路径。