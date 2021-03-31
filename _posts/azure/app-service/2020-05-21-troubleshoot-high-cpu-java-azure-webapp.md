---
layout: post
title: Troubleshoot High CPU - Java - Azure WebApp - Linux
date: 2020-05-21 02:06 +0530
categories: java azure_webapp
---

There are various ways to find what is leading to high CPU usage on any system. Here is something you could do while the issue is going on and you are seeing high CPU usage by a certain Java application hosted on Azure Linux WebApp:

__Install procps:__

The Docker Images for the Java Linux WebApps are based on alpine. You can use apk to install procps:

```bash
apk add procps
```

Please note that the files under /home only persist across WebApp restarts. So, you would need to install procps each time the WebApp restarts.

__Get PID of the Java Process:__

You can use ```ps aux``` or ```jcmd``` to get the PID of the Java Process.

__List the threads:__

Use the following command to list all the Java Threads running in the container:

```bash
top -Hc -p PID
```

After running the top command, you can arrange the threads as per CPU usage using ```Shift + P```

__Get the ID of the thread:__

Now look for the thread which is consuming highest CPU, and note down its PID.

__Collect Thread Dump:__

Collect thread dump using jcmd:

```java
jcmd PID Thread.print > /home/thread.txt
```

__Analyze Thread Dump:__

The Thread Dump contains details about each of the thread running in the JVM. For each thread, we have a NID which is in hexadecimal. In order to co-related this NID with the PID we got earlier, we would need to convert the PID obtained in previous step to hexadecimal value. This can be done easily using Calculator App in Windows. Now, just search for that hexadecimal value in the Thread Dump. [ Note that the hexadecimal values start with __0x__ ]

The corresponding NID will provide the details about the thread consuming high CPU, and also provide the stack trace, which can be useful for finding out the cause of high CPU.

![snapshot]({{ site.baseurl }}/assets/thread-cpu.png)
