* 1.查看进程占用内存：动态 top / pmap -d <PID>   静态 ps -aux
* 2.查看进程的所有线程：pstree -p <PID> / ps -mp <PID / top -H 
* 3.结束后台进程: kill <PID>  终止前台进程 ctrl+c
* 4.启动进程有手动启动和调度启动,调度启动常用命令at / batch / crontab
* 5.swap作用：虚拟内存，当物理内存不够时，将硬盘一部分虚拟成内存使用
* 6.安装linux时，必须有两个分区是：文件系统分区和交换分区(swap)
* 7.内核分为文件管理系统、I/O管理系统、进程调度管理系统、内存管理系统4个子系统
* 8.进程与程序的区别在于其动态性,动态的产生和终止,从产生到终止进程可以具有的基本状态为： 运行态 、 就绪态 和 等待态（阻塞态）
* 9.DNS实际上是分布在internet上的主机信息的数据库,其作用是实现 IP地址和主机名 之间的转换.
* 10.在Linux系统中,用来存放系统所需要的配置文件和子目录的目录是 /etc 
* 11.Links分为 硬链接和符号链接 
* 12.将前一个命令的标准输出作为后一个命令的标准输入,称之为 管道 
* 13.Sendmail邮件系统使用的两个主要协议是： SMTP 和 POP ,前者用来发送邮件,后者用来接收邮件.
* 14. rm命令可删除文件或目录,其主要差别就是是否使用递归开关 -r或-R 
* 15.shell不仅是 用户命令的解释器 ,它同时也是一种功能强大的编程语言. bash是Linux的缺省shell.

[腾讯后台面试题](http://blog.csdn.net/ibmfahsion/article/details/11992403?utm_source=tuicool&utm_medium=referral)

[后端开发面试题](https://github.com/monklof/Back-End-Developer-Interview-Questions)

[BAT面试题](https://github.com/kaiye/kaiye.github.com/issues/3)