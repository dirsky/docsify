

## 理论之JVM的参数类型

1、标配参数
```
-version，其中Java HotSpot
-help
```
    
2、X参数

  java -Xint -version 解释执行
  java -Xcomp -version 第一次使用就编译成本地代码
  java -Xmixed -version 默认，混合模式（先编译再执行）
  
3、XX参数

  Boolean类型： -XX:+ (开启) -XX:- (关闭)，例如 -XX:-PrintGCDetails
  KV设值类型 ： -XX:key=value，           例如 -XX:InitialHeapSize=67108864

https://docs.oracle.com/javase/8/docs/

常用基本配置参数
Eden，s0，s1，OldMemory，元空间
  区别，元空间与之前的永久代功能类似，但永久代使用堆内存，元空间使用本机物理内存。
  堆管存储，栈管运行

## 实践

```
jps -l      
jinfo -flags 12697  # 打印该应用的所有标识，注意有flags包含s，mac下的java8有bug
 jinfo -flag PrintGCDetails 12697         # 查看某一个细项，-XX:-PrintGCDetails  // 表示没有开启这个参数
                                          # idea中设置 VM option 例如,是否打印GC收集细节：-XX:+PrintGCDetails
                                          # 单核cpu使用串行垃圾回收器 -XX:+UseSerialGC
 java -XX:+PrintFlagsInitial              # 查看参数初始化
 java -XX:+PrintFlagsFinal -version | grep ThreadStackSize       # 查看最终的参数，其中有冒号的被修改了
 java -XX:+PrintFlagsFinal -Xss128k T     # T是运行的类的名字
 java -XX:+PrintCommandLineFlags -version # 显示jinfo中的部分内容，主要用来看垃圾回收器，其中7大回收器是4大算法的落地

```

## 经典配置

```shell
-Xms   # 初始JVM大小 默认1/64物理内存，等价-XX:InitialHeapSize=67108864
-Xmx   # JVM大小 默认1/4物理内存 等价MaxHeapSize；
       # -Xms和-Xmx设置的是堆内存，最好设置成一致，避免频繁收集
-Xss   # 初始线程栈内存大小512k-1024k，ThreadStackSize，0使用“系统默认值”。而在HotSpot VM给“系统默认”大小是1MB。
-Xmn   # 年轻代大小（一般不调整）,由jvm大小和NewRatio共同决定；另外年轻代最大MaxNewSize
-XX:MetaspaceSize         # 默认21m，【改】设置元空间大些 -XX:MetaspaceSize=128m
-XX:+PrintGCDetails       # 默认关闭，【改】设置开启打印GC收集细节
-XX:SurvivorRatio         # 默认8，Eden占新生代的比值
-XX:NewRatio              # 默认2，老年代是新生代的倍数，指标是Young:Old= 1:2，即年轻代为老年代的1/2
-XX:MaxTenuringThreshold  # 默认15，新生代代数
```

