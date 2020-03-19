# Docker-MacOs-Network


## Solving the Problem that "Docker Desktop for Mac can’t route traffic to containers"

## Problem

>- **Install a Docker for Mac**
>- **Run a Hadoop image in Docker**
>- **View Hadoop HDFS WebUI by Using Hadoop containers IP**  
```shell  
        docker inspect bbbbb | grep "IPAddress"
```
>![sd](https://drive.google.com/uc?export=view&id=1ppBwjnrhq7ZETDr3dUVG4CEDAi6YTW6d)  

>- **Visit http://172.17.0.2:50070/&emsp; Found out I can't access HDFS WebUI**
>- **Try to ping container ip from mac terminal, it's failed too!**  
>- **Then I checked the official docker documentation and found out the problem** [Docker Documentation](https://docs.docker.com/docker-for-mac/networking/)
>![sd](https://drive.google.com/uc?export=view&id=1qF8wCUtamXxeUDD2yufncMniDXWHzgt3)  
