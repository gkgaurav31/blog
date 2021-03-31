---
layout: post
title: Detect Tomcat Version change on Azure App Service when using Auto-Update...
  and more
date: 2021-03-26 22:08 +0530
categories: tomcat azure_webapp 
---

When using __Auto-Update__ for various stacks on Azure App Service, one of the common problems is to get notified when the version changes. We could follow the official [announcement page](https://github.com/Azure/app-service-announcements/issues) for any major changes. We can also create automation scripts to send notifications when such a change occurs. There are different ways in which this can be done. The idea is to check how the current stack version can be determined, and compare it with the previous versions. If it changes, trigger a notification, which itself can be done in numerous ways. In this blog, I will guide you to one of the several approaches you could take. This may not be the best option, but it will get your work done.

Using [Alerts](https://docs.microsoft.com/en-us/azure/app-service/web-sites-monitor#alerts-and-autoscale) on Azure App Service, you could setup an action to execute an Azure Function, Azure Logic App, Azure Automation etc. When there is a configuration change, the WebApp also restarts. Using these two data points, we could create an alert for __"When the WebApp restarts, run Azure Automation to check the current Tomcat version and see if it has changed. Notify if it did."__

The Kudu REST APIs (uses HTTP Basic Authentication) can be used to execute certain commands on Azure App Service.

__Example:__

```curl
curl --location --request POST 'https://<webappname>.scm.azurewebsites.net/api/command' \
--header 'Authorization: Basic xxxxxxxxxxxx' \
--header 'Content-Type: application/json' \
--header 'Cookie: ARRAffinity=b5a9cc36272cdc0ce98dd1d4591de2a3387e634d23af23b5cfacc2627fff9108; ARRAffinitySameSite=b5a9cc36272cdc0ce98dd1d4591de2a3387e634d23af23b5cfacc2627fff9108' \
--data-raw '{
    "command": '\''echo %AZURE_TOMCAT90_HOME%'\'',
    "dir": '\''site'\''
}'
```

This will return the value of __AZURE_TOMCAT90_HOME__, which contains the Tomcat version as well. The environment variable would be different for different frameworks, stack and even major version of same stack. Now that we have a way to get the Tomcat version, we would just need a way to automate it.

General steps which you would need to follow:  

- Create a Runbook in Azure Automation to detect when the Tomcat version has changed. We could store the current version in a variable. The automation can use the above Kudu REST API to get the current version being used. If both are NOT the same, send a notification.
You could send e-mails using another Azure Automation if needed.
Refer to this documentation: [Send an email from a runbook
](https://docs.microsoft.com/en-us/azure/automation/automation-send-email
).

- Once we have the automation in place, we need a way to trigger it based on certain conditions. App Service alerts can help us here. You could create an Alert which runs when a WebApp is restarted. The alert can also call the Azure Automation as its __action__.

Here is a simple Python 3 runbook script which you could use:

```python
#!/usr/bin/env python3
import requests
import os
import json
import sys
import automationassets

url = 'https://gauktomcatwin.scm.azurewebsites.net/api/command';
myobj = {'command': 'echo %AZURE_TOMCAT90_HOME%', 'dir':'site'};

username = automationassets.get_automation_variable("username");
password = automationassets.get_automation_variable("password");
previous_tomcat_version = automationassets.get_automation_variable("tomcat_version");

response = requests.post(url, json = myobj, auth=(username, password));

current_tomcat_version = response.json()["Output"].split("\\")[2]

automationassets.set_automation_variable("tomcat_version",current_tomcat_version);

if(previous_tomcat_version == current_tomcat_version):
    print ("No Tomcat update. Version: ", current_tomcat_version);
else:
    print("Tomcat version has changed from ", previous_tomcat_version, " to ", current_tomcat_version);
    #more code to send email to notify users
```

With just the above steps, you have a place to check if the Tomcat version has changed. This can be extended to a great extent. Using the same KUDU API, you could run many different commands to check various other configurations.

I hope this helps. :v:
