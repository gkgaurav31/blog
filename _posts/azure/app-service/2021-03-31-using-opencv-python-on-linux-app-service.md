---
layout: post
title: Using opencv-python on Linux App Service
date: 2021-03-31 22:54 +0530
---

The [opencv-python](https://pypi.org/project/opencv-python/) module in Python has certain OS level dependencies, which could be missing in certain environment or Docker images. You could obviously create your own Custom Docker Image and use that to run Python application on Azure WebApp for Containers. But that comes up with an overhead of maintaining the Docker Image. In order to use opencv-python (or any other module which have certain missing dependencies in the environment you are running in), you could make use of a Startup Script to install the required libraries. Using apt install command via webSSH directly would not work, since the files are non-persistent outside of /home. Read more about persistence [HERE](https://docs.microsoft.com/en-us/archive/blogs/waws/things-you-should-know-web-apps-and-linux#if-youre-persisting-files-only-the-home-directory-is-persistedapplies-to-web-app-for-containers)  

So, the idea is to run a startup command/script while the container is coming up as part of the init script. You would need to follow the below steps to achieve this:  

- Create a __mystart.sh__ file in the __/home__ directory. (You could name it anything. Just remember to use the same name later)
- On the Azure Portal of the Web App, under __Configuration > General Settings > StartUp Command__, specify the path of this startup script as __/home/mystart.sh__
- In your mystart.sh, you can add the following lines in case of a flask app:
  
```bash
apt update
apt install libgl1-mesa-glx -y
apt-get install libgomp1 -y
gunicorn --bind=0.0.0.0 --timeout 600 app:app
```

Startup Command for Gunicorn: [LINK](https://docs.microsoft.com/en-us/azure/developer/python/tutorial-deploy-app-service-on-linux-04#django-startup-commands)

You could always add your own commands to the above bash script if you need to install other libraries.

I hope this helps. :v:
