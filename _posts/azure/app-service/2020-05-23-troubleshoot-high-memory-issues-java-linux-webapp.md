---
layout: post
title: Troubleshoot High Memory Issues - Java Linux WebApp
date: 2020-05-23 01:40 +0530
---

There are different ways of dealing with Memory Issues. In this blog, I would like to share some steps you can take in order to find what could be causing memory leak.

It is always recommended to profile and test your application before deploying it to a Production environment. Using jVisualVM is a great way to quickly check the performance of your application.

This would be a good starting point: [jVisualVM](https://visualvm.github.io/documentation.html)

The general symptoms when the memory usage high includes:

- The application slow down overall
- You would see higher rate of major/minor garbage collection
- The Eden space usage would change very frequently

---  

Using [JCMD](https://docs.oracle.com/javase/8/docs/technotes/guides/troubleshoot/tooldescr006.html) - A Swiss-army knife for investigating and resolving issues with your JVM application

This is a a great way to quickly check the details of the JVM, while it is running.

Here are some useful command and flags which you can use:

__Get PID of all the Java Processes:__ jcmd  

__Collect Thread dump:__ jcmd PID Thread.print  

__JVM command line arguments:__ jcmd PID VM.command_line

__Collect Heap Dump:__ jcmd PID GC.heap_dump /home/dump

__Get Heap Memory Information:__ jcmd PID GC.heap_info

---  

### Best Practices and Recommendations  

__Enable Garbage Collection Logs:__  
You can add the following Application Setting for your WebApp to configure GC logs:  
```JAVA_OPTS=-Xloggc:/home/gc.log```

__Collect Heap Dump automatically on OutOfMemoryException:__  
```-XX:+HeapDumpOnOutOfMemoryError -XX:HeapDumpPath=/home/heapdump/```

__Integrate your application with Application Insights:__  
You can use the Application Insights codeless agent to integrate your application with AI. This does not need any code changes:
[Codeless Agent](https://docs.microsoft.com/en-us/azure/azure-monitor/app/java-in-process-agent)

__Use Java Flight Recorder:__  
The recoding can be collected in two way - Timed and Continuous. Continuous recording is helpful when dealing with intermittent issues:
[JFR](https://docs.microsoft.com/en-us/azure/app-service/containers/configure-language-java#flight-recorder)

---

### Other useful links

Using Eclipse Memory Analyzer Tool: [MAT](https://www.youtube.com/watch?v=sLoifF_YA4w)  
Zulu Mission Control: [User Guide](http://docs.azul.com/zmc/ZMCUserGuide/Title.htm)  
Application Insights: [Performance Counters](https://docs.microsoft.com/en-us/azure/azure-monitor/app/java-get-started?tabs=maven#performance-counters)  
Analyze memory dump using jhat: [Documentation](https://docs.oracle.com/javase/8/docs/technotes/tools/unix/jhat.html)