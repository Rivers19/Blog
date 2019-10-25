# Git常用命令清单

## 基础操作命令

- 进入当前路径的dev

    ```bash
      cd dev
    ```

- 返回上级目录

    ```bash
      cd ..
    ```

- 返回上次目录

    ```bash
      cd
    ```

- 查看当前目录

    ```bash
      pwd
    ```

- 列出当前目录内容

    ```shell
      ls    
    ```

- 新建一个名为dev的目录

    ```shell
      mkdir dev
    ```

- 强制删除，不管目录为不为空

    ```shell
      rm -rf dev    
    ```



## 本地工程push到远程

#### 方法一

1. 建立本地仓库

    ```shell
     git init
    ```

2. 创建远程仓库：在github或者gitlab创建远程仓库

3. 让本地仓库和远程仓库进行关联

    ```shell
     git remote add origin git@mycode.skylettestudio.com:xlPay/XLPay.git
    ```

4. 进行添加和提交操作

    ```shell
     git add .
     git commit -m"添加文件"
    ```

5. push到远程

```shell
    git push -u origin master

由于远程库是空的，第一次推送master分支时，加上-u参数，Git不但会把本地的master分支内容推送的远程新的master分支，还会把本地的master分支和远程的master分支关联起来，在以后的推送或者拉取时就可以简化命令，如下：

    git push origin master
```

##### 方式二：

1. 创建远程仓库

2. 把远程仓库clone到本地

    ```bash
     git clone. git@mycode.skylettestudio.com:xlPay/XLPay.git
    ```

3. 将本地工程copy到clone的文件夹中

4. 进行添加和提交操作

    ```shell
     git add -A
     git commit -m"添加文件"
    ```

5. 将工程push到远程

    ```shell
     git push _ origin _ master
    ```

## git clone 指定分支 拉代码

1. git clone 不指定分支

    ```shell
     git clone  http://10.1.1.11/service/tmall-service.git
    ```

2. git clone 指定分支

    ```shell
     git clone -b dev_jk http://10.1.1.11/service/tmall-service.git
    ```

    命令中：多了一个 -b dev-jk,这个dev_jk就是分支，http://10.1.1.11/service/tmall-service.git为源码的仓库地址



## Add操作

- 添加单个改动文件，如：readme.txt

    git add readme.txt

- 添加所有改变的文件，有如下几种区别：

    //表示添加所有内容

    git add -A

    //表示添加新文件和编辑过的文件不包括删除的文件

    git add .

    //表示添加编辑或者删除的文件，不包括新添加的文件

    git add -u

- 添加同时提交内容

    git commit -am"添加并提交到仓库"



## 分支操作

- 查看分支

    ```shell
      git branch
    ```

- 创建分支

    ```shell
      git branch <name>
    ```

- 切换分支

    ```shell
      git checkout <name>
    ```

- 创建+切换分支

    ```shell
      git checkout -b <name>
    ```

- 合并某分支到当前分支

    ```shell
      git merge <name>
    ```

- 删除本地分支

    ```shell
      git branch -d <name>
    ```

- 查看远程分支列表

    ```shell
      git branch -a
    
      绿色代表当前项目所在的分支，红色就是远程分支列表
    ```

- 提交该分支到远程仓库(即：在远程创建dev分支，并提交内容)：

    ```shell
      git push origin dev
    ```

- 从远程获取dev分支内容：

    ```shell
      git pull origin dev
    ```

    或者通过用命令行，运行 git fetch，可以将远程分支信息获取到本地,
    再运行 git checkout -b local-branchname origin/remote_branchname
    就可以将远程分支映射到本地命名为local-branchname 的一分支

- 删除远程分支

    ```shell
      git push origin --delete <branchName>
    ```

- 重命名本地分支

    ```shell
      git branch -m <oldbranch> <newbranch>
    ```

- 重命名远程分支

    ```shell
      先删除远程分支，然后重命名本地分支，再重新提交一个远程分支
    ```



## 版本回退

在Git中，用HEAD表示当前版本。上一个版本就是HEAD，上上一个版本就是HEAD。，当然往上100个版本写100个比较容易数不过来，所以写成HEAD~100。

- 查看提交日志（所有的提交日志，最近到最远）

    ```bash
      git log
    ```

- 查看提交的内容（比如更改了哪些类，删除了哪些文件等）

    ```shell
      git log -p -1// -p 选项展开显示每次提交的内容差异，用 -2 则仅显示最近的两次更新
    ```

- 查看命令历史（即：我们每一个命令）

    ```shell
      git reflog
    ```

- 通过git log 或者 git reflog可以拿到每个版本的commit_id，然后通过切换版本/回退版本的命令即可：

    ```shell
      git reset --hard commit_id
    ```

- 回到上一个版本

    ```shell
      git reset --hard HEAD^
    ```

- 回退版本操作：

    ```shell
      //git log 拿到commit_id
      git log
      //回到commit_id那个版本
      git reset --hard commit_id
    ```

- 已经回退到了之前的版本，又想回到新版本

    ```shell
      //查看所有的命令，然后找到新版本提交的 commit_id
      git reflog
      //去到新版本 
      git reset --hard commit_id
    ```

- 让这个文件回到最近一次git commit或git add时的状态

    ```shell
      git checkout -- readme.txt
    ```

------

## 标签管理

- 打一个新标签

    ```shell
      //默认标签是打在最新提交的commit上的
      git tag <name>
    ```

- 查看所有标签（标签不是按时间顺序列出，而是按字母排序的）

    ```shell
      git tag
    ```

- 查看某个标签信息

    ```shell
      //标签不是按时间顺序列出，而是按字母排序的
      git show <tagname>
    ```

- 创建带有说明的标签，用-a指定标签名，-m指定说明文字

    ```bash
      git tag -a v0.1 -m "设置了标签啊" 3628164
    ```

- 删除本地标签

    ```shell
      git tag -d v0.1
    ```

- 推送一个本地标签到远程

    ```shell
      git push origin <tagname>
    ```

- 推送全部未推送过的本地标签到远程

    ```shell
      git push origin --tags
    ```

- 删除一个远程标签

    ```shell
      //先在本地进行删除
      git push origin :refs/tags/<tagname>
    ```

- tag默认是打在最新的commit上的，如果想给已经commit过的内容添加标签如下：

    ```shell
      1. git reflog //找到历史版本的 commit id = 6224937
      2. git tag v1.0.0 6224937
    ```

- 获取远程tag

    ```shell
      git fetch origin tag <tagname>
    ```



## 解决冲突

- **合并冲突：**

    ```bash
      //提交的文件如果出现冲突就会出现这种提示
      CONFLICT (content): Merge conflict in readme.txt
    
      Automatic merge failed; fix conflicts and then commit the result.
    ```

    1. 通过git status 查看冲突文件

    2. Git用<<<<<<<，=======，>>>>>>>标记出不同分支的内容

    3. 修改文件，保存，再次提交即可

    4. 通过git log查看分支合并的情况

    5. <<<<<<<标记冲突开始，后面跟的是当前分支中的内容。

        HEAD指向当前分支末梢的提交。

        =======之后，>>>>>>>之前是要merge过来的另一条分支上的代码。

        \>>>>>>>之后的dev是该分支的名字。



## 更新本地工程

- 获取远端库最新信息

    git fetch origin

- 作比较

    ```shell
      git diff master origin/master
    ```

- 合并本地仓库代码

    ```shell
      git merge origin/master
    ```

- git pull 和 git pull --rebase 区别

    [不错的图解](https://links.jianshu.com/go?to=https%3A%2F%2Fwww.cnblogs.com%2Fkevingrace%2Fp%2F5896706.html)



## 公共操作

- 把本地git仓库恢复为普通文件夹

    1. 删除这个目录里的隐藏文件夹.git
    2. rm -rf .git/

- 测试Github和Gitlab是否添加了SSH

    ```shell
      ssh -T git@github.com
      ssh -T git@gitlab.com
    ```

- 查看未传送到远程代码库的提交描述/说明（commit到了本地仓库，但是没有push到远程仓库的内容的提交说明）

    ```shell
      git cherry -v origin thcdev//后面要加push到的远程仓库名
    ```

- 查看远程库信息

    ```shell
      git remote -v；
    ```

- 清屏操作

    ```shell
      git reset
    ```

- git log之后退出

    ```shell
      按 Q 即可
    ```

- 找到历史提交的commit id

    ```bash
      git log --pretty=oneline --abbrev-commit
    ```

- 和远程仓库建立联系

    1. 创建SSH Key

        - 在用户主目录（这里在windows下是指 c/user/Administrator/.ssh/id_rsa）下，看看有没有.ssh目录。

        - 如果有，再看看这个目录下有没有id_rsa和id_rsa.pub这两个文件，如果已经有了，可直接跳到下一步。

        - 如果没有，打开Shell（Windows下打开Git Bash），创建SSH Key：

            ```shell
              $ ssh-keygen -t rsa -C "youremail@example.com"
            ```

        - 需要把邮件地址换成你自己的邮件地址，然后一路回车，使用默认值即可。

        - 由于这个Key也不是用于军事目的，所以也无需设置密码。

        - （测试的结果：C:\Users\Administrator.ssh 里面有id_rsa和id_rsa.pub两个文件，这两个就是SSH Key的秘钥对，id_rsa
            是私钥，不能泄露出去，id_rsa.pub是公钥，可以放心地告诉任何人。）

    2. 登陆GitHub，打开“Account settings”，“SSH Keys”页面：然后，点“Add SSH Key”，填上任意Title，在Key文本框里粘贴id_rsa.pub文件的内容。点“Add Key”，你就应该看到已经添加的Key：

        **注意两点：**

        - 为什么GitHub需要SSH Key呢？因为GitHub需要识别出你推送的提交确实是你推送的，而不是别人冒充的，而Git支持SSH协议，所以，GitHub只要知道了你的公钥，就可以确认只有你自己才能推送。
        - GitHub允许你添加多个Key。假定你有若干电脑，你一会儿在公司提交，一会儿在家里提交，只要把每台电脑的Key都添加到GitHub，就可以在每台电脑上往GitHub推送了。

    3. 测试SSH key是否添加成功

        输入如下命令进行测试

        ```shell
         ssh -T git@github.com
        ```

        如果出现：

        ![image-20191025165608129](https://rivers19-1300325434.cos.ap-beijing.myqcloud.com/2019-10-25-090416.png)

        说明添加成功了。



## stash

```shell
git stash //回滚到修改前并暂存修改内容 

git stash apply //从stash list中拿到应用stash中的内容，但是stash还会存在stash list中

git stash pop //从stash list中拿到应用stash中的内容，会从stash list中删除stash

git stash list // 显示所有的stash内容

git stash clear //清空stash list中所有stash

git stash drop stash@{0}  //这是删除第一个队列
```



## 多人协作的工作模式

- 首先，可以试图用git push origin <branch-name>推送自己的修改；

- 如果推送失败，则因为远程分支比你的本地更新，需要先用git pull试图合并；

- 如果合并有冲突，则解决冲突，并在本地提交；

- 没有冲突或者解决掉冲突后，再用git push origin <branch-name>推送就能成功！

- 如果git pull提示no tracking information，则说明本地分支和远程分支的链接关系没有创建，用命令git branch --set-upstream-to <branch-name> origin/<branch-name>。

    这就是多人协作的工作模式，一旦熟悉了，就非常简单。