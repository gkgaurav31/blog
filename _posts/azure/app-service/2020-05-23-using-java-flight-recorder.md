---
layout: post
title: Using Java Flight Recorder
date: 2020-05-23 02:46 +0530
categories: java jfr performance
---

#### You can now use Java Flight Recorder on Azure App Services - Linux!  

[Announcement Page](https://azure.microsoft.com/en-in/updates/java-on-app-service-updates-and-improvements/)

#### What is JFR

> Java Flight Recorder (JFR) is a tool for collecting diagnostic and profiling data about a running Java application. It is integrated into the Java Virtual Machine (JVM) and causes almost no performance overhead, so it can be used even in heavily loaded production environments. When default settings are used, both internal testing and customer feedback indicate that performance impact is less than one percent. For some applications, it can be significantly lower. However, for short-running applications (which are not the kind of applications running in production environments), relative startup and warmup times can be larger, which might impact the performance by more than one percent. JFR collects data about the JVM as well as the Java application running on it.

#### Benefits

- Low Overhead; Can be run in a prod environment as well
- Continuous recording is possible

#### Java Mission Control (JMC)

- Set of tools using which we can analyze JFR
- It's a desktop application and runs on top of Eclipse
- Can be connected to a remote JVM via JMX (Java Management Extensions)
- JMX console can connect to the JVM and provide data in real time. We can create rules based on certain events to collect JFR for example.

#### How to Enable JFR

JFR can be enabled using the following JVM arguments:  
```java -XX:+UnlockCommercialFeatures -XX:+FlightRecorder```

#### Types of recordings

- Timed Fixed Recording
- Continuous Recording: uses a buffer (configurable size) to continuously collect information. once the buffer is full, old data gets discarded

#### JFR Event Settings

By default there are two configurations are present for the JRF recordings:

- Continuous — default.jfc - has less overhead
- Profiling — profile.jfc

#### NOTE

We can run multiple recordings at the same time, but the same buffer will be used by the JFR runtime. The final output will be the union of all the data which has been collected from all the recordings.

#### Commands

- Timed-Recording  
```jcmd 116 JFR.start name=MyRecording settings=profile duration=30s filename="/home/jfr_example.jfr"```  

- Continuous Recording  

  - Add the following Application Setting:  
```JAVA_OPTS=-XX:StartFlightRecording=disk=true,name=continuous_recording,dumponexit=true,maxsize=1024m,maxage=1d```

  - Once the recording has started, you can dump the current recording data at any time using the JFR.dump command.  
```jcmd <pid> JFR.dump name=continuous_recording filename="/home/recording1.jfr"```

#### References

- Using JFR - [Zulu](https://docs.azul.com/zing/UseZVM_JavaFlightRecorder_Overview.htm)
- Zulu Mission Control: [Documentation](http://docs.azul.com/zmc/ZMCUserGuide/Title.htm)
- JFR on Linux App Service - Azure: [Documentation](https://docs.microsoft.com/en-us/azure/app-service/containers/configure-language-java#flight-recorder)