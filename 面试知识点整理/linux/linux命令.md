# linux命令

- ps命令（process status）
  - 用于显示当前进程的状态，类似于windows的任务管理器
  - 查看所有java进程 `ps -ef | grep java`
  - https://www.runoob.com/linux/linux-comm-ps.html
  
- 停止进程命令
  - 停止特定java进程命令：`kill -9 java进程序号`
  - 停止所有java进程命令：`pkill - 9 java`
  
- 查看当前机器负载：Linux的负载高，主要是由于CPU使用、内存使用、IO消耗三部分构成。任意一项使用过多，都将导致服务器负载的急剧攀升。
  - `uptime`
  - `w`
  - ![image-20201122174307800](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\image-20201122174307800.png)
  - load average分别对应于过去1分钟，5分钟，15分钟的负载平均值。
  
- `top命令能够清晰的展现出系统的状态，而且它是实时的监控，按q退出。`
  
  - ![img](https://img-blog.csdn.net/20140809122156703?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvc3pjaHR4/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center)
  
- netstat：用来查看端口情况

  - netstat -ntlp//查看当前所有的tcp端口
  
  - lsof -i:端口 显示占用该端口的进程情况
  
  - ```
    netstat -ntlp   //查看当前所有tcp端口
    netstat -ntulp | grep 80   //查看所有80端口使用情况
    netstat -ntulp | grep 3306   //查看所有3306端口使用情况
    ```
  
- 打开指定端口

- awk命令：是一种处理文本文件的语言，是一个强大的文本分析工具

- linux查看日志：ex分页查询

- linux chmod https://blog.csdn.net/hudashi/article/details/7797393

  - 文件权限：在Linux中，创建一个文件时，该文件的拥有者都是创建该文件的用户。该文件用户可以修改该文件的拥有者及用户组，当然root用户可以修改任何文件的拥有者及用户组。在Linux中，对于文件的权限（rwx），分为三部分，一部分是该文件的拥有者所拥有的权限，一部分是该文件所在用户组的用户所拥有的权限，另一部分是其他用户所拥有的权限
  - 在shell中，要修改文件当前的用户必须具有管理员root的权限。可以通过su命令切换到root用户，也可以通过sudo获得root的权限。

- 如何更改linux文件的拥有者及用户组

  - 在 shell 中，可以使用chown命令来改变文件所有者。chown命令是change owner（改变拥有者）的缩写。需要要注意的是，用户必须是已经存在系统中的，也就是只能改变为在 /etc/passwd这个文件中有记录的用户名称才可以

  - chown [-R] 账号名称 文件或目录

    chown [-R] 账号名称:用户组名称 文件或目录

  - -R : 进行递归( recursive )的持续更改，即连同子目录下的所有文件、目录

    都更新成为这个用户组。常常用在更改某一目录的情况。

- 