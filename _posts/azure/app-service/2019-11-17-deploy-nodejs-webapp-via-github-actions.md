---
layout: post
title: Deploy NodeJS WebApp via GitHub Actions
date: 2019-11-17 00:41 +0530
---

# Steps to deploy a NodeJS application on Azure App Service

[GitHub Actions](https://github.com/features/actions) provides a simple way to setting up a pipeline to build and deploy your applications.
There are pre-configured / templates to deploy directly to Azure App Services which makes it even easier.

__The major steps are:__  
    

- Create a new repository on GitHub and push your code. Here is a sample code which you can use:
[https://github.com/gkgaurav31/nodejs-github-actions](https://github.com/gkgaurav31/nodejs-github-actions)

- Go to your repository on GitHub and click on Actions. Create a New Workflow and search for __Deploy Node.js to Azure Web App__ 

- In the template that comes up, update the WebApp Name, NodeJS Version and the location of your application (default is root, which you should be fine for this sample app)

- Now, GitHub needs a way to get authorization to deploy the application to Azure WebApp.  
This will require a publish profile which will be set as a secret. Follow the below steps:  
    
    - Go to your WebApp's Overview blade and click on __"Publish Profile"__. Copy the contents of this Publish Profile which gets downloaded.  

    - Go to your __GitHub Repository -> Settings -> Secrets__
    
    - Create a new secret called __AZURE_WEBAPP_PUBLISH_PROFILE__. Paste the content of the publish profile as the value.

- That's it! Commit the changes, which will trigger this pipeline and the application should get deployed.  

  

__Useful Links:__
- [https://azure.github.io/AppService/2019/08/10/Github-actions-for-webapps.html](https://azure.github.io/AppService/2019/08/10/Github-actions-for-webapps.html)
- [https://github.com/Azure/Actions](https://github.com/Azure/Actions)
- [https://github.com/Azure/actions-workflow-samples](https://github.com/Azure/actions-workflow-samples)

__Sample YAML Configuration File__

```yml
# This workflow will build and push a node.js application to an Azure Web App on every push to the master branch.
#
# To configure this workflow:
#
# 1. Set up a secret in your repository named AZURE_WEBAPP_PUBLISH_PROFILE with the value of your Azure publish profile.
#
# 2. Change the values for the AZURE_WEBAPP_NAME, AZURE_WEBAPP_PACKAGE_PATH and NODE_VERSION environment variables  (below).
#
# For more information on GitHub Actions for Azure, refer to https://github.com/Azure/Actions
# For more samples to get started with GitHub Action workflows to deploy to Azure, refer to https://github.com/Azure/actions-workflow-samples
on:
  push:
    branches:
      - master

env:
  AZURE_WEBAPP_NAME: myWebApp    # set this to your application's name
  AZURE_WEBAPP_PACKAGE_PATH: '.'      # set this to the path to your web app project, defaults to the repository root
  NODE_VERSION: '10.14.1'                # set this to the node version to use

jobs:
  build-and-deploy:
    name: Build and Deploy
    runs-on: ubuntu-latest
    steps:
    - uses: actions/checkout@master
    - name: Use Node.js ${{ env.NODE_VERSION }}
      uses: actions/setup-node@v1
      with:
        node-version: ${{ env.NODE_VERSION }}
    - name: npm install, build, and test
      run: |
        # Build and test the project, then
        # deploy to Azure Web App.
        npm install
        npm run build --if-present
        npm run test --if-present
    - name: 'Deploy to Azure WebApp'
      uses: azure/webapps-deploy@v1
      with:
        app-name: ${{ env.AZURE_WEBAPP_NAME }}
        publish-profile: ${{ secrets.AZURE_WEBAPP_PUBLISH_PROFILE }}
        package: ${{ env.AZURE_WEBAPP_PACKAGE_PATH }}
```