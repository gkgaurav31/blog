# Here is a way on how you can stop and remove all containers which are currenly running:

```
- Stop all containers:
docker stop $(docker ps -a -q)

- Remove all stopped contains:
docker rm $(docker ps -a -q)
```