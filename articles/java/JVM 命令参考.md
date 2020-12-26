<h1> JVM 命令参考 </h1>

---

**Table of Contents**

- [jps 显示出所有的 JAVA 进程以及 PID](#jps-显示出所有的-java-进程以及-pid)
- [jstat 查看堆内存各部分的使用量，以及加载类的数量](#jstat-查看堆内存各部分的使用量以及加载类的数量)
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
		* 一个极强的监视VM内存工具。可以用来监视VM内存内的各种堆和非堆的大小及其内存使用量。
		* 使用
			jstat [参数] [pid] [interval] [count]
			
			参数		//必须值
			pid			//JAVA线程的PID
			interval	//隔多少毫秒刷新一次
			count		//一共刷新多少次,不写就死循环
			
		* 参数
			-gcutil	