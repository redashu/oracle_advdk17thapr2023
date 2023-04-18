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

## Container Security context 

### giving security by blocking root user 

```
[ashu@ip-172-31-31-88 ashu-images]$ cd python/
[ashu@ip-172-31-31-88 python]$ ls
Dockerfile  alpine.dockerfile  ashu.py
[ashu@ip-172-31-31-88 python]$ docker build -t  ashupy:v2 -f alpine.dockerfile  . 
Sending build context to Docker daemon  4.608kB
Step 1/8 : FROM alpine
latest: Pulling from library/alpine
f56be85fc22e: Pull complete 
Digest: sha256:124c7d2707904eea7431fffe91522a01e5a861a624ee31d03372cc1d138a3126
Status: Downloaded newer image for alpine:latest
 ---> 9ed4aefc74f6
Step 2/8 : LABEL name=ashutoshh
 ---> Running in 5efd6b191adc
Removing intermediate container 5efd6b191adc
 ---> 379adfa3cd8c
Step 3/8 : RUN apk add python3 && mkdir /code
 ---> Running in 998bf8a449b1
fetch https://dl-cdn.alpinelinux.org/alpine/v3.17/main/x86_64/APKINDEX.tar.gz
fetch https://dl-cdn.alpinelinux.org/alpine/v3.17/community/x86_64/APKINDEX.tar.gz
(1/13) Installing libbz2 (1.0.8-r4)
(2/13) Installing libexpat (2.5.0-r0)
(3/13) Installing libffi (3.4.4-r0)
(4/13) Installing gdbm (1.23-r0)
(5/13) Installing xz-libs (5.2.9-r0)
(6/13) Installing libgcc (12.2.1_git20220924-r4)
(7/13) Installing libstdc++ (12.2.1_git20220924-r4)
(8/13) Installing mpdecimal (2.5.1-r1)
(9/13) Installing ncurses-terminfo-base (6.3_p20221119-r0)
(10/13) Installing ncurses-libs (6.3_p20221119-r0)
(11/13) Installing readline (8.2.0-r0)
(12/13) Installing sqlite-libs (3.40.1-r0)
(13/13) Installing python3 (3.10.11-r0)
Executing busybox-1.35.0-r29.trigger
OK: 60 MiB in 28 packages
Removing intermediate container 998bf8a449b1
 ---> d75a2a7b0fed
Step 4/8 : ADD https://raw.githubusercontent.com/redashu/pythonLang/main/while.py /code/
Downloading     232B
[ashu@ip-172-31-31-88 python]$ 
[ashu@ip-172-31-31-88 python]$ 
[ashu@ip-172-31-31-88 python]$ 
[ashu@ip-172-31-31-88 python]$ docker images
REPOSITORY   TAG       IMAGE ID       CREATED          SIZE
ashupy       v2        2c0d4a94a495   22 seconds ago   58.8MB
alpine       latest    9ed4aefc74f6   2 weeks ago      7.05MB
[ashu@ip-172-31-31-88 python]$ docker run -itd --name c1 ashupy:v2 
97d14b3ad7cb44b9436bcb55b0d15d4b22dec7152e57de8dde5dad3697c884d1
[ashu@ip-172-31-31-88 python]$ docker ps
CONTAINER ID   IMAGE       COMMAND                  CREATED         STATUS        PORTS     NAMES
97d14b3ad7cb   ashupy:v2   "python3 /code/while…"   2 seconds ago   Up 1 second             c1
[ashu@ip-172-31-31-88 python]$ docker logs c1
Hello all , welcome to python..!!
Welcome to LnB..
Welcome to Containers ..!!
______________________
Hello all , welcome to python..!!
Welcome to LnB..
Welcome to Containers ..!!
______________________
[ashu@ip-172-31-31-88 python]$ docker logs c1 -f
Hello all , welcome to python..!!
Welcome to LnB..
Welcome to Containers ..!!
______________________
Hello all , welcome to python..!!
Welcome to LnB..
Welcome to Containers ..!!
______________________
Hello all , welcome to python..!!
Welcome to LnB..
Welcome to Containers ..!!
______________________
Hello all , welcome to python..!!
^C
```

### security container by remove shell from container image

```
FROM alpine 
LABEL name=ashutoshh
RUN apk add python3 && mkdir /code 
ADD https://raw.githubusercontent.com/redashu/pythonLang/main/while.py /code/
# copy vs add is add for copy data from URL 
RUN chmod 644  /code/while.py && adduser -D jack 
RUN rm -rf /bin/sh 
# adding jack user
USER jack 
# calling jack user 
ENTRYPOINT ["python3"] 
CMD ["/code/while.py"] 
# REplacement of CMD and also having few difference 
# as last argument it is not replacable 

```

### check 

```
230  docker run -itd --name c2 ashupy:v3 
  231  docker ps
  232  docker logs -f c2
  
[ashu@ip-172-31-31-88 python]$ docker  exec -it c2 sh
OCI runtime exec failed: exec failed: unable to start container process: exec: "sh": executable file not found in $PATH: unknown
[ashu@ip-172-31-31-88 python]$ 
```

### demo 

```
  48  nsenter -t  15176  --uts --mount   bash 
   49  nsenter -t  15176  --uts --mount  sh 
   50  nsenter -t  15176  --uts --mount ip a
   51  nsenter -t  15176  --uts --mount ifconfig 
   52  nsenter -t  15176  --uts --mount whoami
   53  nsenter -t  15176  --uts --mount mkdir  /okkk
   54  nsenter -t  15176  --uts --mount ls /
   55  nsenter -t  15176  --uts --mount  ls  /code
   56  nsenter -t  15176  --net  ifconfig 
   57  nsenter -t  15176  --net --uts --mount   ifconfig 
   58  nsenter -t  15176  --net --uts --mount   ip a
   59  nsenter -t  15176   --uts --mount   ip a
   60  nsenter -t  15176   --uts --mount   ifconfig 
   61  nsenter -t  15176   --net   ifconfig 
   62  history 
[root@ip-172-31-31-88 ~]# ps -e  | grep python 
  15176 ?        00:00:00 python3
  19152 ?        00:00:00 python3
```



