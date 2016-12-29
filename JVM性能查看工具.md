#JVM性能查看工具
##1.jstat
	作用：利用jvm内建的指令对java应用程序的资源和性能进行实时的命令行监控，包括针对heap size和垃圾回收情况的监控。
	用法： jstat [option] pid
	option:	
		-class:统计class loader行为信息 
		-compile：统计编译行为信息 
		-gc：统计jdk gc时heap信息 
		-gccapacity：统计不同的generations（包括新生区，老年区，permanent区）相应的heap容量情况 
		-gccause：统计gc的情况，（同-gcutil）和引起gc的事件 
		-gcnew：统计gc时，新生代的情况 
		-gcnewcapacity：统计gc时，新生代heap容量 
		-gcold：统计gc时，老年区的情况 
		-gcoldcapacity：统计gc时，老年区heap容量 
		-gcpermcapacity：统计gc时，permanent区heap容量 
		-gcutil：统计gc时，heap情况
### -gc
	[root@wangxu-180 bin]# jstat -gc 21371  1000 2  #<=间隔为1s，重复出现两次~
 	S0C    S1C    S0U    S1U      EC       EU        OC         OU       MC     MU    CCSC   CCSU   YGC     YGCT    FGC    FGCT     GCT   
	10240.0 10240.0 9303.5  0.0   81920.0  45348.6   159744.0   56841.3   37888.0 36902.9 4608.0 4349.1     14    0.735   1      0.004    0.740
	10240.0 10240.0 9303.5  0.0   81920.0  45348.6   159744.0   56841.3   37888.0 36902.9 4608.0 4349.1     14    0.735   1      0.004    0.740
	参数含义：
	S0C:年轻代的第一个survicor(幸存区)的容量(字节)
	S1C:年轻代的第二个survicor(幸存区)的容量(字节)
	S0U:年轻代的第一个survicor(幸存区)已经使用的空间(字节)
	S1U:年轻代的第二个survicor(幸存区)已经使用的空间(字节)
	EC:年轻代中的Eden(伊甸园)的容量
	EU:年轻代中的Een(伊甸园)已经使用的容量
 	OC:年老代的容量
	OU:年老代已经使用的空间
	MC:metadata的容量
	MU:metadata已经使用的空间
	CCSC:压缩类空间的大小
	CCSU:压缩类空间的使用大小
	YG:从应用程序启动到采样时年轻代中gc次数
	YGCT:从应用程序启动到采样时年轻代中gc所用时间(s)
	FGC:从应用程序启动到采样时年老代gc次数
	FGCT:从应用程序启动到采样时年老代(全gc)gc所用时间(s)
	GC:	从应用程序启动到采样时gc用的总时间(s)






##2.jmap
	作用：查看jvm的内存分配情况
	用法：jmap [option] pid
- heap：打印堆的内存信息
- dump：

		[root@iauth02 java_bin]# jmap -heap 12249
		Attaching to process ID 12249, please wait...
		Debugger attached successfully.
		Server compiler detected.
		JVM version is 25.77-b03

		using thread-local object allocation.
		Parallel GC with 2 thread(s)

		Heap Configuration:  #堆内存初始化设置
 		 MinHeapFreeRatio         = 0		#<= -XX:MinHeapFreeRatio=设置JVM堆最小空闲比率
 	 	 MaxHeapFreeRatio         = 100     #<= -XX:MaxHeapFreeRatio=设置JVM堆最大空闲比率
 		 MaxHeapSize              = 1006632960 (960.0MB)  #<=-XX:MaxHeapSize=JVM堆最大内存大小
  	 	 NewSize                  = 20971520 (20.0MB)     #<= -XX:NewSize=设置JVM堆新生代内存的默认大小
  		 MaxNewSize               = 335544320 (320.0MB)   #<= -XX:MaxNewSize=设置JVM堆新生代内存的最大大小
  		 OldSize                  = 41943040 (40.0MB)     #<= -XX:OldSize=设置JVM堆的老年代大小
  		 NewRatio                 = 2					  #<= -XX:NewRatio=:新生代和老生代的大小比率
  		 SurvivorRatio            = 8					  #<= -XX:SurvivorRatio=设置年轻代中Eden区与Survivor区的大小比值 
  	 	MetaspaceSize            = 21807104 (20.796875MB) #<= -XX:MetaspaceSize= 设置元空间的初始大小
  		 CompressedClassSpaceSize = 1073741824 (1024.0MB)
   		MaxMetaspaceSize         = 17592186044415 MB      #<= -XX:MetaspaceSize= 设置元空间的最大大小
   		G1HeapRegionSize         = 0 (0.0MB)

		Heap Usage:
		PS Young Generation
		Eden Space:            #<=eden区内存分布
   		capacity = 202375168 (193.0MB)
  		used     = 164582424 (156.9580307006836MB)
 		free     = 37792744 (36.041969299316406MB)
  		 81.32540450812621% used
		From Space:            #<=survivor0区内存分布
  		capacity = 2097152 (2.0MB)
  		used     = 1676768 (1.599090576171875MB)
  		free     = 420384 (0.400909423828125MB)
  		79.95452880859375% used
		To Space:			  #<=survivor1区内存分布
   		capacity = 2097152 (2.0MB)
  		 used     = 0 (0.0MB)
  		 free     = 2097152 (2.0MB)
  		 0.0% used
		PS Old Generation     #<=老生代区内存分布
  		 capacity = 186646528 (178.0MB)
  		 used     = 112483992 (107.2730941772461MB)
  		 free     = 74162536 (70.7269058227539MB)
  		 60.265783245643874% used

			30512 interned Strings occupying 3600128 bytes.



##3.jstack
	作用：查看java程序的线程运行情况
	用法：jstack [选项] pid
	常用：jstack pid 
		注意：如果java程序是在tomcat用户下启动的，一定必须在对应的tomcat用户下执行该命令

	[root@wangxu-180 bin]# jps
	7879 Jps
	21371 
	[root@wangxu-180 bin]# su - tomcat --shell=/bin/bash -c 'jstack 21371' 2>/dev/null >/home/wangxu/java.txt



##4.jps
	查看jvm进程的pid


##5.jinfo
	[root@iauth01 wangxu]# jps
	9810 
	11400 Jps
	[root@iauth01 wangxu]# jinfo -flags  9810
	Attaching to process ID 9810, please wait...
	Debugger attached successfully.
	Server compiler detected.
	JVM version is 25.77-b03
	Non-default VM flags: -XX:CICompilerCount=2 -XX:InitialHeapSize=1006632960 -XX:MaxHeapSize=1006632960 -XX:MaxMetaspaceSize=230686720 -XX:MaxNewSize=335544320 -XX:MetaspaceSize=230686720 -XX:MinHeapDeltaBytes=524288 -XX:NewSize=335544320 -XX:OldSize=671088640 -XX:SurvivorRatio=8 -XX:+UseCompressedClassPointers -XX:+UseCompressedOops -XX:+UseParallelGC 
	Command line:  -Djava.util.logging.config.file=/usr/local/apache-tomcat-7.0.73/conf/logging.properties -Xmx960m -Xms960m -XX:NewSize=320m -XX:MaxNewSize=320m -XX:SurvivorRatio=8 -XX:MetaspaceSize=220m -XX:MaxMetaspaceSize=220m -Djava.util.logging.manager=org.apache.juli.ClassLoaderLogManager -javaagent:/usr/local/apache-tomcat-7.0.73/tingyun/tingyun-agent-java.jar -Djava.endorsed.dirs= -Dcatalina.base=/usr/local/apache-tomcat-7.0.73 -Dcatalina.home=/usr/local/apache-tomcat-7.0.73 -Djava.io.tmpdir=/usr/local/apache-tomcat-7.0.73/temp -Dcommons.daemon.process.id=9810 -Dcommons.daemon.process.parent=9809 -Dcommons.daemon.version=1.0.15-dev abort

		