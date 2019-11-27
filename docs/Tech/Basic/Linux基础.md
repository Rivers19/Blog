# Linux基础



## 系统目录结构

- 登录系统后输入，可看到一级目录

    ```shell
    ls /
    ```

- 树状目录：

![image-20191127164827907](https://rivers19-1300325434.cos.ap-beijing.myqcloud.com/2019-11-27-084828.png)

- **/root**： 该目录为系统管理员，也称作超级权限者的用户主目录。
- **/bin**： bin是Binary的缩写, 这个目录存放着最经常使用的命令。

- **/boot：** 这里存放的是启动Linux时使用的一些核心文件，包括一些连接文件以及镜像文件。
- **/dev ：** dev是Device(设备)的缩写, 该目录下存放的是Linux的外部设备，在Linux中访问设备的方式和访问文件的方式是相同的。

- **/etc：** 这个目录用来存放所有的系统管理所需要的配置文件和子目录。

- **/home**： 用户的主目录，在Linux中，每个用户都有一个自己的目录，一般该目录名是以用户的账号命名的。

- **/var**： 这个目录中存放着在不断扩充着的东西，我们习惯将那些经常被修改的目录放在这个目录下。包括各种日志文件。

- **/lib**： 这个目录里存放着系统最基本的动态连接共享库，其作用类似于Windows里的DLL文件。几乎所有的应用程序都需要用到这些共享库。

- **/usr**： 这是一个非常重要的目录，用户的很多应用程序和文件都放在这个目录下，类似于windows下的program files目录。

- **/sys**： 这是linux2.6内核的一个很大的变化。该目录下安装了2.6内核中新出现的一个文件系统 sysfs 。

    sysfs文件系统集成了下面3种文件系统的信息：针对进程信息的proc文件系统、针对设备的devfs文件系统以及针对伪终端的devpts文件系统。

    该文件系统是内核设备树的一个直观反映。

    当一个内核对象被创建的时候，对应的文件和目录也在内核对象子系统中被创建。

- **/tmp**： 这个目录是用来存放一些临时文件的。

> 在 Linux 系统中，有几个目录是比较重要的，平时需要注意不要误删除或者随意更改内部文件。
>
> **/etc**： 上边也提到了，这个是系统中的配置文件，如果你更改了该目录下的某个文件可能会导致系统不能启动。
>
> **/bin, /sbin, /usr/bin, /usr/sbin**: 这是系统预设的执行文件的放置目录，比如 ls 就是在/bin/ls 目录下的。
>
> 值得提出的是，/bin, /usr/bin 是给系统用户使用的指令（除root外的通用户），而/sbin, /usr/sbin 则是给root使用的指令。
>
> **/var**： 这是一个非常重要的目录，系统上跑了很多程序，那么每个程序都会有相应的日志产生，而这些日志就被记录到这个目录下，具体在/var/log 目录下，另外mail的预设放置也是在这里。

在Linux文件系统中有两个特殊的目录，一个用户所在的工作目录，也叫当前目录，可以使用一个点 **.** 来表示；另一个是当前目录的上一级目录，也叫父目录，可以使用两个点 **..** 来表示。

-  . ：代表当前的目录，也可以使用 ./ 来表示；
-  .. ：代表上一层目录，也可以 ../ 来代表。

如果一个目录或文件名以一个点 . 开始，表示这个目录或文件是一个隐藏目录或文件(如：.bashrc)。即以默认方式查找时，不显示该目录或文件。s

## 文件和目录管理

### 绝对/相对路径

- **绝对路径：**
    路径的写法，由根目录 / 写起，例如： /usr/share/doc 这个目录。
- **相对路径：**
    路径的写法，不是由 / 写起，例如由 /usr/share/doc 要到 /usr/share/man 底下时，可以写成： cd ../man 



### 目录相关常用命令

- ls: 列出目录

    ```shell
    [root@www ~]# ls [-aAdfFhilnrRSt] 目录名称
    [root@www ~]# ls [--color={never,auto,always}] 目录名称
    [root@www ~]# ls [--full-time] 目录名称
    ```

    选项与参数：

    - -a ：全部的文件，连同隐藏档( 开头为 . 的文件) 一起列出来(常用)
    - -d ：仅列出目录本身，而不是列出目录内的文件数据(常用)
    - -l ：长数据串列出，包含文件的属性与权限等等数据；(常用)

    将家目录下的所有文件列出来(含属性与隐藏档)

    ```shell
    [root@www ~]# ls -al ~
    ```

- cd：切换目录

    ```shell
    #使用 mkdir 命令创建 runoob 目录
    [root@www ~]# mkdir runoob
    
    #使用绝对路径切换到 runoob 目录
    [root@www ~]# cd /root/runoob/
    
    #使用相对路径切换到 runoob 目录
    [root@www ~]# cd ./runoob/
    
    # 表示回到自己的家目录，亦即是 /root 这个目录
    [root@www runoob]# cd ~
    
    # 表示去到目前的上一级目录，亦即是 /root 的上一级目录的意思；
    [root@www ~]# cd ..
    ```

- pwd：显示目前的目录

    ```shell
    [root@www ~]# pwd [-P]
    ```

- mkdir：创建一个新的目录

    ```shell
    mkdir [-mp] 目录名称
    ```

    选项与参数：

    - -m ：配置文件的权限喔！直接配置，不需要看默认权限 (umask) 的脸色～
    - -p ：帮助你直接将所需要的目录(包含上一级目录)递归创建起来！

    实例：请到/tmp底下尝试创建数个新目录看看：

    ```shell
    [root@www ~]# cd /tmp
    [root@www tmp]# mkdir test    <==创建一名为 test 的新目录
    [root@www tmp]# mkdir test1/test2/test3/test4
    mkdir: cannot create directory `test1/test2/test3/test4': 
    No such file or directory       <== 没办法直接创建此目录啊！
    [root@www tmp]# mkdir -p test1/test2/test3/test4
    ```

    加了这个 -p 的选项，可以自行帮你创建多层目录！

- rmdir：删除一个空的目录

    ```shell
    [root@www tmp]# rmdir test   <==可直接删除掉，没问题
    [root@www tmp]# rmdir test1  <==因为尚有内容，所以无法删除！
    rmdir: `test1': Directory not empty
    [root@www tmp]# rmdir -p test1/test2/test3/test4
    [root@www tmp]# ls -l        <==您看看，底下的输出中test与test1不见了！
    drwx--x--x  2 root  root 4096 Jul 18 12:54 test2
    ```

    利用 -p 这个选项，立刻就可以将 test1/test2/test3/test4 一次删除。

    不过要注意的是，这个 rmdir 仅能删除空的目录，你可以使用 rm 命令来删除非空目录。

    

- cp: 复制文件或目录

- rm: 移除文件或目录

- mv: 移动文件与目录，或修改文件与目录的名称

> 可以使用 *man [命令]* 来查看各个命令的使用文档，如 ：man cp。

