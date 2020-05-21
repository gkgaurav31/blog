---
layout: post
title: Troubleshoot - JVM Crash - Azure WebApp - Linux
date: 2020-05-22 00:09 +0530
---

On Azure WebApp Linux, if your JVM is crashing and is unable to come up, you would see a generic "Application Error" message when trying to browse your site. In order to find what might be causing this issue, we could need to determine if the JVM is crashing. If yes, then we can collect the crash dump to find out the root cause of the issue.

### STEPS

__Enable App Service Logs:__

In order to get the stdout/stderr logs for the WebApp, you will need to enable container logs for the WebApp.

__Check logs:__

Now, you should be able to access the logs via Log Stream from Azure Portal. This can also be accessed via the Kudu Site (accessible via __Advanced Tools__ blade of the WebApp)
You can also use the following Azure CLI command to stream the logs on your local terminal:

```cli
az webapp log tail -g RESOURCE_GROUP -n WEBAPP_NAME
```

__Analyze the logs:__

If you see something like the following image, it indicates that the JVM is crashing while trying to come up:

![snapshot]({{ site.baseurl }}/assets/jvm-crash.png)

__Collect the Crash Dump:__

On Linux WebApp, by default, the files under /home only persist. So, we would need to configure our JVM to dump the logs to /home.
This can be done by using the JVM argument: -XX:ErrorFile=/home/hs_err_pid%p.log. In order to configure this on the WebApp, add the following Application Setting via Configuration blade of the WebApp:

```java
JAVA_OPTS=-XX:ErrorFile=/home/hs_err_pid%p.log
```

![snapshot]({{ site.baseurl }}/assets/app-setting-java-opts.png)

The easiest way to access the log file generated is by going to the Kudu site from Advanced Tools blade of the WebApp -> Files -> Open the link which would look like:  

```https://<webappname>.scm.azurewebsites.net/api/vfs/hs_err_pid123.log```.  

The crash dump will indicate the reason why JVM is failing to come up. If the problem is due to memory, you could try configure the heap memory using the following JVM argument: ```-Xms1024m -Xmx1024m```. This will set the minimum and maximum heap memory for the JVM to 1 GB.
