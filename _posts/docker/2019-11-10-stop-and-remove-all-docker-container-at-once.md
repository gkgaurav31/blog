---
layout: post
title: Stop and remove all Docker containers at once
date: 2019-11-10 22:27 +0530
---

# Here is a way on how you can stop and remove all containers which are currenly running:

```
- Stop all containers:
docker stop $(docker ps -a -q)

- Remove all stopped contains:
docker rm $(docker ps -a -q)
```