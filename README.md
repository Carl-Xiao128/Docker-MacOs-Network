# Docker-MacOs-Network
## Solving the Problem that Docker Desktop for Mac can’t route traffic to containers and You can access the containers by IP right away in MacOs platform!

### Problem

>- Install a Docker for Mac
>- Run a Hadoop image in Docker
>- View Hadoop HDFS WebUI by Using Hadoop containers IP  
```shell  
        docker inspect bbbbb | grep "IPAddress"
```
>![](https://drive.google.com/uc?export=view&id=1ppBwjnrhq7ZETDr3dUVG4CEDAi6YTW6d)  

>- Visit `http://172.17.0.2:50070/`&emsp; Found out I can't access HDFS WebUI
>- Try to ping container ip from mac terminal, it's failed too!  
>- Then I checked the official docker documentation and found out the problem [Docker Documentation](https://docs.docker.com/docker-for-mac/networking/)
>![](https://drive.google.com/uc?export=view&id=1qF8wCUtamXxeUDD2yufncMniDXWHzgt3)  
>- If you want to connect a container from the Mac, you need to use `Port Mapping` like below
```shell  
        docker run -d –it -p 50070 -p 8088 --name bbbbb sequenceiq/hadoop-docker:2.7.1
```
>- But I found some other problems, such as  `can't open log file`, `can't download Hadoop output result file.` from WebUI directly! You have to reach those file by using terminal command, Troublesome！


## Here is the solution!
>- Using Docker for Mac an experimental function: `Socket Proxy`
> #### &emsp; 1. `Open Mac terminal` and `install jq`
```shell  
        brew install jq
```
> #### &emsp; 2. `Change Docker configuration`
```shell  
        cd ~/Library/Group\ Containers/group.com.docker/
        mv settings.json settings.json.backup
        cat settings.json.backup | jq '.["socksProxyPort"]=8888' > settings.json
```
> #### &emsp; 3. `Restart Docker for Mac`
> #### &emsp; 4. Go `System Preference` -> `Network` -> `Advanced` ->
> #### &emsp;&ensp; `Proxies` ->  Select `Automatic Proxy Configuration` ->
> #### &emsp;&ensp; Type `http://localhost/socket.pac` into the URL
>![](https://drive.google.com/uc?export=view&id=1Uzq2tmLo0tbROemx6e_HC_myfc6tvLsZ)
> #### &emsp; 5. `Start Mac local Apache service`
```shell  
        sudo apachectl start
```
> #### &emsp; 6. `Check Apache service status`, Visit `http://localhost`,
> #### &emsp;&ensp; it's started successfully if the browser shows "It works!"
>![](https://drive.google.com/uc?export=view&id=1GH2c6l6uVlpVR1croAfqXGu35MCVe4hq)
> #### &emsp; 7. `Create Pac file`, File name : `socket.pac`, content below
```shell  
        function FindProxyForURL(url, host) {
        if (isInNet(host, '172.17.0.0', '255.255.0.0')) {
        return 'SOCKS5 127.0.0.1:8888'
        }
        if (isInNet(host, '172.22.0.0', '255.255.0.0')) {
        return 'SOCKS5 127.0.0.1:8888'
        }
        return 'DIRECT'
}
```
> #### &emsp; 8. Put  `socket.pac` file in directory `/Library/WebServer/Documents`
> #### &emsp;&ensp; and Visit `http://localhost/socket.pac`, if the browser shows:
>![](https://drive.google.com/uc?export=view&id=1iWN58Sw_JohC0k2xrbe21ZKpnNceKe8w)
> #### &emsp; 9. `Check the containers hosts`
```shell  
        cat /etc/hosts
```
>![](https://drive.google.com/uc?export=view&id=1pMP0MB-ka4ziAKz_st7uv3j6jMEXsyD3)
> #### &emsp; 10. `Modify the MacOs hosts File`
>![](https://drive.google.com/uc?export=view&id=1qeO3ooLLLoBVX1NpL_ij2i9omrs1RPzG)
> #### &emsp; 11. `The Socket Proxy configuration is finished!`
> #### &emsp; 12. `Run Hadoop Docker image`
>![](https://drive.google.com/uc?export=view&id=1UjnhMVrB7869I8IQAKSCG_7yHZtpE-J9)
>![](https://drive.google.com/uc?export=view&id=10WEDOUMnMjmCN7nitbzDMuvvZjgVJIQu)
> #### &emsp; 13. `Visiting HDFS WebUI and Resource Manager WebUI have two ways`
>>- ##### &emsp; `By Port Mapping!`
```shell  
        http://localhost:32769
        http://localhost:32768
```
>>- ##### &emsp; `By containers IP right away!`
```shell  
        http://172.17.0.2:8088
        http://172.17.0.2:50070
```
## The benefits of using containers IP to access WebUI
>- `It won't occupy your Mac local Port`
>- `All WebUI support invoke each other, such as`  
>> - &emsp; `ResourceManager Web UI: 8088`  
>> - &emsp; `NodeManager Web UI: 8042`   
>> - &emsp; `NameNode Web UI: 50070`   
>> - &emsp; `DataNode Web UI: 50075`  
>> - &emsp; `Secondary NameNode Web UI: 50090`  
>> - &emsp; `JobTracker Web UI: 50030`
>> - &emsp; `TaskTracker Web UI: 50060`
>-  `Looking at current status of HDFS, explore file system`
>-  `Exploring file system, download HDFS file directly`
>-  `Checking status and log file`  

![](https://drive.google.com/uc?export=view&id=1Gn09ivZH4XZO1McvialW0_XNbYG4KMbI)  

![](https://drive.google.com/uc?export=view&id=1JtUXrx2Dph86-WL3daDJ-iTjRmuNPQTu)

![](https://drive.google.com/uc?export=view&id=1divlbiJHa2UrG1s8fSZ_wzqw2JeT0RDb)

## Referenced Documentation
[D1]&emsp;<https://docs.docker.com/docker-for-mac/networking/>  
[D2]&emsp;<https://windmt.com/2019/08/30/docker-for-mac-network/>  
[D3]&emsp;<https://www.jianshu.com/p/d006a34a343f>
