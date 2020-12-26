<h1> JVM 命令参考 </h1>

---

**Table of Contents**

- [jps 显示出所有的 JAVA 进程以及 PID](#jps-显示出所有的-java-进程以及-pid)
- [jstat 查看堆内存各部分的使用量，以及加载类的数量](#jstat-查看堆内存各部分的使用量以及加载类的数量)
- [jstack – 用来查看堆栈信息](#jstack--用来查看堆栈信息)
---

# jps 显示出所有的 JAVA 进程以及 PID

显示出所有的 JAVA 进程以及 PID.
当未指定 hostid 时，默认查看本机jvm 进程，否者查看指定的hostid机器上的jvm进程，此时hostid所指机器必须开启jstatd服务。 jps 可以列出jvm进程lvmid，主类类名，main函数参数, jvm参数，jar名称等信息。

```s
usage: jps [-help]
       jps [-q] [-mlvV] [<hostid>]

Definitions:
    <hostid>:      <hostname>[:<port>]
```

**参数**

- -q 仅仅显示VM 标示，不显示jar,class, main参数等信息.
- -l 输出应用程序主类完整package名称或jar完整名称 
- -v 显示出jvm参数
- -m 显示出传递给main函数的参数
- -V 输出通过.hotsportrc或-XX:Flags=<filename>指定的jvm参数
- hostid 指定特定主机，可以是ip地址和域名, 也可以指定具体协议，端口。

**示例**

```
# jps
93793
95554 Jps
93860 Application
95439 Application
527
95438 Launcher

# jps -q
93793
93860
95628
95439
527
95438

# jps -l
93793
93860 /Users/zhangyunan/Library/Application
95439 com.example.aries.Application
95678 sun.tools.jps.Jps
527
95438 org.jetbrains.jps.cmdline.Launcher

jps -lvmV
95793 sun.tools.jps.Jps -lvmV -Dapplication.home=/Library/Java/JavaVirtualMachines/jdk1.8.0_144.jdk/Contents/Home -Xms8m
93793  -Xms128m -Xmx2000m -XX:ReservedCodeCacheSize=240m -XX:+UseConcMarkSweepGC -XX:SoftRefLRUPolicyMSPerMB=50 -ea -XX:CICompilerCount=2 -Dsun.io.useCanonPrefixCache=false -Djdk.http.auth.tunneling.disabledSchemes="" -XX:+HeapDumpOnOutOfMemoryError -XX:-OmitStackTraceInFastThrow -Djdk.attach.allowAttachSelf=true -Dkotlinx.coroutines.debug=off -Djdk.module.illegalAccess.silent=true -XX:+UseCompressedOops -Dfile.encoding=UTF-8 -XX:ErrorFile=/Users/zhangyunan/java_error_in_idea_%p.log -XX:HeapDumpPath=/Users/zhangyunan/java_error_in_idea.hprof -Dide.no.platform.update=true -Didea.plugins.path=/Users/zhangyunan/Library/Application Support/JetBrains/Toolbox/apps/IDEA-C/ch-0/203.5981.155/IntelliJ IDEA CE.app.plugins -Djb.vmOptionsFile=/Users/zhangyunan/Library/Application Support/JetBrains/Toolbox/apps/IDEA-C/ch-0/203.5981.155/IntelliJ IDEA CE.app.vmoptions -Didea.paths.selector=IdeaIC2020.3 -Didea.executable=idea -Didea.platform.prefix=Idea -Didea.vendor.name=JetBrains -Didea.home.path=/Users/zhangyunan/Library/Applicat
93860 /Users/zhangyunan/Library/Application Support/JetBrains/Toolbox/apps/IDEA-C/ch-0/203.5981.155/IntelliJ IDEA CE.app.plugins/idea-spring-tools/lib/server/language-server.jar
95439 com.example.aries.Application -javaagent:/Users/zhangyunan/Library/Application Support/JetBrains/Toolbox/apps/IDEA-C/ch-0/203.5981.155/IntelliJ IDEA CE.app/Contents/lib/idea_rt.jar=64466:/Users/zhangyunan/Library/Application Support/JetBrains/Toolbox/apps/IDEA-C/ch-0/203.5981.155/IntelliJ IDEA CE.app/Contents/bin -Dfile.encoding=UTF-8
527  -Djava.library.path=/Applications/JetBrains Toolbox.app/Contents/MacOS/Contents -Duser.dir=/Applications/JetBrains Toolbox.app/Contents/MacOS -Xmx256m -Xms8m -Xss256k -XX:+UseStringDeduplication -XX:+UseCompressedOops -XX:+UseSerialGC -Djava.awt.headless=true -Djava.net.preferIPv4Stack=true vfprintf exit abort -DTOOLBOX_VERSION=1.19.7784
95438 org.jetbrains.jps.cmdline.Launcher /Users/zhangyunan/Library/Application Support/JetBrains/Toolbox/apps/IDEA-C/ch-0/203.5981.155/IntelliJ IDEA CE.app/Contents/lib/jna-platform.jar:/Users/zhangyunan/Library/Application Support/JetBrains/Toolbox/apps/IDEA-C/ch-0/203.5981.155/IntelliJ IDEA CE.app/Contents/lib/jna.jar:/Users/zhangyunan/Library/Application Support/JetBrains/Toolbox/apps/IDEA-C/ch-0/203.5981.155/IntelliJ IDEA CE.app/Contents/lib/asm-all-9.0.jar:/Users/zhangyunan/Library/Application Support/JetBrains/Toolbox/apps/IDEA-C/ch-0/203.5981.155/IntelliJ IDEA CE.app/Contents/lib/maven-resolver-impl-1.3.3.jar:/Users/zhangyunan/Library/Application Support/JetBrains/Toolbox/apps/IDEA-C/ch-0/203.5981.155/IntelliJ IDEA CE.app/Contents/lib/guava-29.0-jre.jar:/Users/zhangyunan/Library/Application Support/JetBrains/Toolbox/apps/IDEA-C/ch-0/203.5981.155/IntelliJ IDEA CE.app/Contents/plugins/java/lib/jps-javac-extension-1.jar:/Users/zhangyunan/Library/Application Support/JetBrains/Toolbox/apps/IDEA-C/ch-0/203.5981.15 -Xmx700m -Djava.awt.headless=true -Djava.endorsed.dirs="" -Djdt.compiler.useSingleThread=true -Dpreload.project.path=/Users/zhangyunan/IdeaProjects/aries-java -Dpreload.config.path=/Users/zhangyunan/Library/Application Support/JetBrains/IdeaIC2020.3/options -Dcompile.parallel=false -Drebuild.on.dependency.change=true -Dio.netty.initialSeedUniquifier=2915071559679537949 -Dfile.encoding=UTF-8 -Duser.language=zh -Duser.country=CN -Didea.paths.selector=IdeaIC2020.3 -Didea.home.path=/Users/zhangyunan/Library/Application Support/JetBrains/Toolbox/apps/IDEA-C/ch-0/203.5981.155/IntelliJ IDEA CE.app/Contents -Didea.config.path=/Users/zhangyunan/Library/Application Support/JetBrains/IdeaIC2020.3 -Didea.plugins.path=/Users/zhangyunan/Library/Application Support/JetBrains/Toolbox/apps/IDEA-C/ch-0/203.5981.155/IntelliJ IDEA CE.app.plugins -Djps.log.dir=/Users/zhangyunan/Library/Logs/JetBrains/IdeaIC2020.3/build-log -Djps.fallback.jdk.home=/Users/zhangyunan/Library/Application Support/JetBrains/Toolbox/apps/IDEA-C/ch-0/203
```

# jstat 查看堆内存各部分的使用量，以及加载类的数量

```s
Usage: jstat -help|-options
       jstat -<option> [-t] [-h<lines>] <vmid> [<interval> [<count>]]

jstat [-命令选项] [vmid] [间隔时间/毫秒] [查询次数]

Definitions:
  <option>      An option reported by the -options option
  <vmid>        Virtual Machine Identifier. A vmid takes the following form:
                     <lvmid>[@<hostname>[:<port>]]
                Where <lvmid> is the local vm identifier for the target
                Java virtual machine, typically a process id; <hostname> is
                the name of the host running the target Java virtual machine;
                and <port> is the port number for the rmiregistry on the
                target host. See the jvmstat documentation for a more complete
                description of the Virtual Machine Identifier.
  <lines>       Number of samples between header lines.
  <interval>    Sampling interval. The following forms are allowed:
                    <n>["ms"|"s"]
                Where <n> is an integer and the suffix specifies the units as
                milliseconds("ms") or seconds("s"). The default units are "ms".
  <count>       Number of samples to take before terminating.
  -J<flag>      Pass <flag> directly to the runtime system.
```

jstat [-命令选项] [-h标题之前的内容行数] [vmid] [间隔时间/毫秒] [查询次数]

**其中 option 可选及其示例：**

- class 显示加载class的数量，及所占空间等信息

```
Loaded  Bytes  Unloaded  Bytes     Time
  7961 14216.6        0     0.0       3.67
```
- compiler 显示VM实时编译的数量等信息

```
Compiled Failed Invalid   Time   FailedType FailedMethod
    4156      1       0    12.33          1 org/springframework/boot/loader/jar/Handler openConnection
```

- gc 显示gc的信息，查看gc的次数，及时间。其中最后五项，分别是 young gc 的次数，young gc的时间，full gc的次数，full gc的时间，gc的总时间

```
 S0C    S1C    S0U    S1U      EC       EU        OC         OU       MC     MU    CCSC   CCSU   YGC     YGCT    FGC    FGCT     GCT
12288.0 12288.0  0.0    0.0   206848.0 119412.2  140800.0   13896.3   35416.0 32942.8 4736.0 4310.2      7    0.081   2      0.080    0.161
```

- jstat -gccapacity:可以显示，VM内存中三代（young,old,perm）对象的使用和占用大小，如：PGCMN显示的是最小perm的内存使用量，PGCMX显示的是perm的内存最大使用量，PGC是当前新生成的perm内存占用量，PC是但前perm内存占用量。其他的可以根据这个类推， OC是old 内存的占用量。

```
 NGCMN    NGCMX     NGC     S0C   S1C       EC      OGCMN      OGCMX       OGC         OC       MCMN     MCMX      MC     CCSMN    CCSMX     CCSC    YGC    FGC
 87040.0 1397760.0 363008.0 12288.0 12288.0 206848.0   175104.0  2796544.0   140800.0   140800.0      0.0 1079296.0  35416.0      0.0 1048576.0   4736.0      7     2
```

- gcnew new对象的信息

```
 S0C    S1C    S0U    S1U   TT MTT  DSS      EC       EU     YGC     YGCT
12288.0 12288.0    0.0    0.0  4  15 12288.0 206848.0 119412.2      7    0.081
```

- gcnewcapacity new对象的信息及其占用量

```
  NGCMN      NGCMX       NGC      S0CMX     S0C     S1CMX     S1C       ECMX        EC      YGC   FGC
   87040.0  1397760.0   363008.0 465920.0  12288.0 465920.0  12288.0  1396736.0   206848.0     7     2
```
- gcold old对象的信息

```
   MC       MU      CCSC     CCSU       OC          OU       YGC    FGC    FGCT     GCT
 35416.0  32942.8   4736.0   4310.2    140800.0     13896.3      7     2    0.080    0.161
```
- gcoldcapacity old对象的信息及其占用量

```
   OGCMN       OGCMX        OGC         OC       YGC   FGC    FGCT     GCT
   175104.0   2796544.0    140800.0    140800.0     7     2    0.080    0.161
```

- gcutil 统计gc信息统计

```
  S0     S1     E      O      M     CCS    YGC     YGCT    FGC    FGCT     GCT
  0.00   0.00  61.23   9.87  93.02  91.01      7    0.081     2    0.080    0.161
```

- printcompilation 当前VM执行的信息。
除了以上一个参数外，还可以同时加上 两个数字，

```
Compiled  Size  Type Method
    4272   1558    1 java/util/TreeMap put
```

以上为 jstat -option vmid 示例，这种每次只会获取当时的信息，如果想多次获取，可以配合 interval 参数来获取，例如：每隔 1000 毫秒获取一次 vmid 为 95980 的 gcutil 信息，总共获取 7 次，其中每 2 次样本间打印下表头 `jstat -gcutil -h2 95980 1000 7`

```s
jstat -gcutil -h2 95980 1000 7
  S0     S1     E      O      M     CCS    YGC     YGCT    FGC    FGCT     GCT
  0.00   0.00  61.23   9.87  93.02  91.01      7    0.081     2    0.080    0.161
  0.00   0.00  61.23   9.87  93.02  91.01      7    0.081     2    0.080    0.161
  S0     S1     E      O      M     CCS    YGC     YGCT    FGC    FGCT     GCT
  0.00   0.00  61.23   9.87  93.02  91.01      7    0.081     2    0.080    0.161
  0.00   0.00  61.23   9.87  93.02  91.01      7    0.081     2    0.080    0.161
  S0     S1     E      O      M     CCS    YGC     YGCT    FGC    FGCT     GCT
  0.00   0.00  61.23   9.87  93.02  91.01      7    0.081     2    0.080    0.161
  0.00   0.00  61.23   9.87  93.02  91.01      7    0.081     2    0.080    0.161
  S0     S1     E      O      M     CCS    YGC     YGCT    FGC    FGCT     GCT
  0.00   0.00  61.23   9.87  93.02  91.01      7    0.081     2    0.080    0.161
```

		

# jstack – 用来查看堆栈信息