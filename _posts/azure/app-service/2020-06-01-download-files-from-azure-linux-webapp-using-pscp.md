---
layout: post
title: Download files from Azure Linux WebApp using pscp
date: 2020-06-01 20:06 +0530
---

There are multiple ways of downloading files from Azure WebApps, some of which include:

- [FTP](https://docs.microsoft.com/en-us/azure/app-service/deploy-ftp)
- [Kudu VFS API](https://github.com/projectkudu/kudu/wiki/REST-API#vfs)

There is another way, which does not require you to login to the Azure Portal, and it can be used for any Docker container which has SSH enabled.  

:notebook_with_decorative_cover: Refer to the following documentation on how to configure SSH: [Documentation](https://docs.microsoft.com/en-us/azure/app-service/containers/configure-custom-container#enable-ssh)

:exclamation: __NOTE:__ The documentation contains an example, and the actual commands/configurations would also depend on the base image being used in the Dockerfile.

---  

Here is how you can use pscp to copy files to a WebApp for Containers (Linux) on Azure:

- Create a remote connection using a tcp tunnel to your web app:  
```az webapp create-remote-connection -g RESOURCE_GROUP -n APP_NAME -p 2222```

- Then copy the required files using pscp:
[ [PSCP](https://www.ssh.com/ssh/putty/putty-manuals/0.68/Chapter5.html) is part of the PuTTY tool suite for Windows ]  
```pscp -P 2222 root@localhost:/home/test.log .```

:exclamation: __NOTE:__ You would need to provide the root password here. This is usually set using the following directive:

```RUN echo "root:Docker!" | chpasswd```

That's it! :v:
