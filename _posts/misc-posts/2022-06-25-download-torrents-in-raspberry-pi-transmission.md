---
layout: post
title: Download Torrents in Raspberry Pi - Transmission
date: 2022-06-25 14:59 +0530
---

## Download Torrents in Raspberry Pi using Transmission

### INSTALLATION & CONFIGURATION

- sudo apt install transmission-daemon

- sudo systemctl start transmission-daemon

- sudo systemctl enable transmission-daemon
(auto-start on reboot)

- sudo nano /etc/transmission-daemon/settings.json

- allow your IP address:
  
```text
"rpc-whitelist": "127.0.0.1,192.168.0.*"
```

Access the UI:
http://<your local IP address>:9091

Default username/password: transmission/transmission

That's it. :v:
