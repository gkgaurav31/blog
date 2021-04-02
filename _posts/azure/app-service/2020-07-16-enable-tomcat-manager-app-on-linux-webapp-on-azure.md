---
layout: post
title: Enable Tomcat Manager app on Linux WebApp on Azure
date: 2020-07-16 15:20 +0530
categories: tomcat azure_webapp
---

Tomcat Manager application is a simple way to deploy WAR files on Tomcat Server. Although, the default Linux WebApp which uses the Tomcat image does not have the manager app pre-installed, we can do it using the steps metioned in this blog. It should be okay to use the Manager App when running on a single instance. However, it is recommended to use [wardeploy API](https://github.com/projectkudu/kudu/wiki/Deploying-WAR-files-using-wardeploy#why-wardeploy).

#### STEPS

##### Upload manager app

- Copy the manager application from your local system to the WebApp's /home/site/wwwroot/webapps directory

- Manager app has a configuration that restricts access only to ___localhost___. We can remove that restriction by removing the following configuration from context.xml of the manager application:

##### Allow access from any IP

```xml
  <Valve className="org.apache.catalina.valves.RemoteAddrValve"
         allow="127\.\d+\.\d+\.\d+|::1|0:0:0:0:0:0:0:1" />
```

##### Copy Tomcat to /home

- We would also need to add a user under Tomcat's conf/tomcat-users.xml file. Since the files under only /home persist on a Linux WebApp, we can create a copy of Tomcat's configuration under /home. The container has the logic to use Tomcat under /home is present:

```cmd
mkdir -p /home/tomcat
cp -a /usr/local/tomcat/conf /home/tomcat/conf
```

##### Add a user for Tomcat Manager

Edit the tomcat-users.xml file under /home/tomcat/conf and add the following:

```xml
  <role rolename="manager-gui"/>
  <role rolename="manager-script"/>
  <user username="tomcatgui" password="s3cret" roles="manager-gui"/>
  <user username="tomcattext" password="mypassword" roles="manager-script"/>
```

##### Restart the WebApp

That's all. :v:

You should be able to access Tomcat Manager app via webappname.azurewebsites.net/manager URL. Use the username/password configured in the previous steps to login.
