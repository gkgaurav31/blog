---
layout: post
title: Collecting Thread Dump on Azure WebApp
date: 2019-11-10 21:26 +0530
categories: java dump azure_webapp
---

# Below are some of the ways using which you can collect Thread Dump on Azure WebApps (Windows)

- Using Diagnose & Solve Problems blade of the WebApp:
  - As of now, this works for the Java Versions for which JDK is available on the platform. You can directly go to "Diagnose & Solve Problems" blade and search for "Thread Dump" which will show up the tool to collect Java Thread Dumps.  
  - The benefit of using this technique is that you can also get an analysis or report of the dump directly on the Azure Portal.  

- The 2nd way is by manually uploading a compatible version of JDK to the kudu site and then running the jstack command to collect the dump. The major steps are:
  - Upload JDK via the Kudu site or FTP
  - Get the PID of the java process from Process Explorer tab of the kudu site
  - Use the following command to collect the dump:

```cmd
jstack.exe -F PID >> output.txt
```

Alternative Method using jcmd:

```cmd
jcmd PID Thread.print >> output.txt
```
