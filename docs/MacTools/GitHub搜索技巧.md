# GitHub搜索技巧





### 1. 搜索仓库名称

in:name **关键词**



### 2. 搜索仓库描述

in:description **关键词**



### 3. 搜索README文件

in:readme **关键词**



### 4. 限制star和fork数量

- stars:> **数字** **关键词**
    - eg. stars:>1000  machine learning
- stars: **10..20** **关键词**
    - 要找在指定数字区间时使用
    - 如果不加 >= 的话，是要精确找 star 数等于具体数字的，这个一般有点困难。

- fork同理



### 5. 限制仓库大小

size:>= **数字** **关键词**

这里注意下，这个数字代表K, 3000代表着3M。



### 6. 仓库是否在更新、仓库什么时间创建的

- pushed:>2019-01-01 deep learning
    - 2019年元旦之后还在更新的deep learning 项目

- created:>2019-01-01 deep learning
    - 2019年元旦之后创建的deep learning项目



### 7. 确定仓库的LICENSE

license:apache-2.0 spring cloud



### 8. 确定仓库的语言

​	language:python **关键词**



### 9. 确定搜索具体个人或组织的仓库

- user:rivers19
    - 搜索作者的GitHub仓库

- org:buaa
    - 搜索北航的GitHub仓库



**以上可根据具体需求**  **组合使用**，**注意多个查询之间以空格分隔**

