### target of the day 

<img src="tg.png">


### Revision 

### docker server store images in given format 

```
[root@ip-172-31-31-88 ~]# cd   /var/lib/docker
[root@ip-172-31-31-88 docker]# ls
buildkit  containers  image  network  overlay2  plugins  runtimes  swarm  tmp  trust  volumes
[root@ip-172-31-31-88 docker]# cd  image/
[root@ip-172-31-31-88 image]# ls
overlay2
[root@ip-172-31-31-88 image]# cd overlay2/
[root@ip-172-31-31-88 overlay2]# ls
distribution  imagedb  layerdb  repositories.json
[root@ip-172-31-31-88 overlay2]# cd imagedb/
[root@ip-172-31-31-88 imagedb]# ls
content  metadata
[root@ip-172-31-31-88 imagedb]# cd content/
[root@ip-172-31-31-88 content]# ls
sha256
[root@ip-172-31-31-88 content]# cd sha256/
[root@ip-172-31-31-88 sha256]# ls
014535df7ae3634d6aedf8e94be5f7abcc080a9588632caf4f3d10cbb852ef40
064991a0ba73a32b9170ce500a25c6f7bacff002b93f9ee496834d138382e91a
06a36ca3206d695b904f50009e43f226fc8efc20a9a8d6245d8e625999ea9f81
08b096c935ed67159802a8d849af7d2c5eda19408ca0931
```

### replacing entrypoint based parent process 

```
docker run -it --name c1 --entrypoint  sleep 100   alpine
```

### Docker client & server model

<img src="ds.png">

### Creating custom bridge with subnet 

```
root@ip-172-31-31-88 ~]# docker network create br3  --subnet  192.169.100.0/24 
7db7cfdff8221a84bdff71c56e93e5632eb3e766cdaac0e650a9169ff1a34d9f
```

### giving static ip to container 

```
[root@ip-172-31-31-88 ~]# docker run -itd --name c9  --network br3  alpine 
c622bef8e15e9ef36a80ab0f4b31bc6f3c1f9f3c617c5532c23d4ee9a9cde3f0
[root@ip-172-31-31-88 ~]# docker exec -it c9 ifconfig 
eth0      Link encap:Ethernet  HWaddr 02:42:C0:A9:64:02  
          inet addr:192.169.100.2  Bcast:192.169.100.255  Mask:255.255.255.0
          UP BROADCAST RUNNING MULTICAST  MTU:1500  Metric:1
          RX packets:12 errors:0 dropped:0 overruns:0 frame:0
          TX packets:0 errors:0 dropped:0 overruns:0 carrier:0
          collisions:0 txqueuelen:0 
          RX bytes:1112 (1.0 KiB)  TX bytes:0 (0.0 B)


```

## Introduction to Rancher Desktop 

<img src="rancher.png">

### rancher desktop prerequisite 

<img src="pre.png">

### Rancher Desktop reality 

<img src="ran.png">



