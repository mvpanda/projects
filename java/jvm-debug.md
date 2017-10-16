# jvm-debug
## 预热知识
* jvm
* 垃圾回收

##  jps（Java Virtual Machine Process Status Tool）
jps主要用来输出JVM中运行的进程状态信息。语法格式如下：

jps [options] [hostid]

```shell
-q 不输出类名、Jar名和传入main方法的参数
-m 输出传入main方法的参数
-l 输出main类或Jar的全限名
-v 输出传入JVM的参数
```

## jstack
 jstack主要用来查看某个Java进程内的线程堆栈信息。语法格式如下：
 
 jstack [option] pid
 
 ```shell
 -l long listings，会打印出额外的锁信息，在发生死锁时可以用jstack -l pid来观察锁持有情况
-m mixed mode，不仅会输出Java堆栈信息，还会输出C/C++堆栈信息（比如Native方法）
```
#### 实例
1. 找出进程中最耗费cpu的线程：top -Hp pid
2. 找到线程id并转换成十六进制值：printf "%x\n" thread_id
3. 找到线程堆栈信息：jstack pid | grep thread_id_ox

## jmap（Memory Map）和jhat（Java Heap Analysis Tool）
jmap用来查看堆内存使用状况，一般结合jhat使用。语法格式如下：

jmap [option] pid

jhat -port port_num dump_file

```shell
jmap -permstat pid  # 打印进程的类加载器和类加载器加载的持久代对象信息
jmap -heap pid  # 查看进程堆内存使用情况，包括使用的GC算法、堆配置参数和各代中堆内存使用情况
jmap -histo[:live] pid  # 查看堆内存中的对象数目、大小统计直方图，如果带上live则只统计活对象
```

```java
B  byte
C  char
D  double
F  float
I  int
J  long
Z  boolean
[  数组，如[I表示int[]
[L+类名 其他对象
```

```shell
jmap -dump:format=b,file=/tmp/dump.dat pid
jhat -port 9998 /tmp/dump.dat
# 浏览器打开： http://ip:port
```

## jstat（JVM统计监测工具）
语法格式如下：

jstat [ generalOption | outputOptions vmid [interval[s|ms] [count]] ]

vmid是虚拟机ID，在Linux/Unix系统上一般就是进程ID。interval是采样时间间隔。count是采样数目。
```shell
jstat -gc vimid 1000 4
```
