# ps

ps命令将某个进程显示出来

grep命令是查找

中间的|是管道命令 是指ps命令与grep同时执行

PS是LINUX下最常用的也是非常强大的进程查看命令

以下这条命令是检查java 进程是否存在：ps -ef |grep java

字段含义如下：

```
UID       PID       PPID      C     STIME    TTY       TIME         CMD

zzw      14124   13991      0     00:38      pts/0      00:00:00    grep --color=auto dae
```
UID      ：程序被该 UID 所拥有

PID      ：就是这个程序的 ID 

PPID    ：则是其上级父程序的ID

C          ：CPU使用的资源百分比

STIME ：系统启动时间

TTY     ：登入者的终端机位置

TIME   ：使用掉的CPU时间。

CMD   ：所下达的是什么指令