## Docker Revision -- freshing 

### Container vs vm 

<img src="vmc.png">

### Isolation and resources limits in containers using Namesapce & contorl groups 

<img src="cgns.png">

### labs -- 

<img src="lab.png">

### Installing docker in any LInux platform 

### checking linux kernel -- 3.10==>

```
[ec2-user@ip-172-31-31-88 ~]$ uname -r
6.1.21-1.45.amzn
```

### Docker ce vs docker ee

<img src="ce.png">

### Installing Docker ce on rhel|centos|oraclelinux|amazonlinux

```
[root@ip-172-31-31-88 ~]# yum install docker 
Last metadata expiration check: 0:03:23 ago on Mon Apr 17 04:43:55 2023.
Dependencies resolved.
==========================================================================================================================
 Package                            Architecture       Version                              Repository               Size
==========================================================================================================================
Installing:
 docker                             x86_64             20.10.17-1.amzn2023.0.6              amazonlinux              39 M
Installing dependencies:
 containerd                         x86_64             1.6.19-1.amzn2023.0.1                amazonlinux              31 M
 iptables-libs                      x86_64             1.8.8-3.amzn2023.0.2                 amazonlinux             401 k
 iptables-nft                       x
```

### starting docker engine service 

```
[root@ip-172-31-31-88 ~]# systemctl start  docker 
[root@ip-172-31-31-88 ~]# systemctl status  docker 
● docker.service - Docker Application Container Engine
     Loaded: loaded (/usr/lib/systemd/system/docker.service; disabled; preset: disabled)
     Active: active (running) since Mon 2023-04-17 04:59:01 UTC; 5s ago
TriggeredBy: ● docker.socket

```

### checking docker installation and conenct

```
[ashu@ip-172-31-31-88 ~]$ docker  version 
Client:
 Version:           20.10.17
 API version:       1.41
 Go version:        go1.19.3
 Git commit:        100c701
 Built:             Mon Mar 13 22:41:42 2023
 OS/Arch:           linux/amd64
 Context:           default
 Experimental:      true
Got permission denied while trying to connect to the Docker daemon socket at unix:///var/run/docker.sock: Get "http://%2Fvar%2Frun%2Fdocker.sock/v1.24/version": dial unix /var/run/docker.sock: connect: permission denied
[ashu@ip-172-31-31-88 ~]$ 


```

### adding ashu user in docker group

```
usermod -G  docker ashu 
[root@ip-172-31-31-88 ~]# 
[root@ip-172-31-31-88 ~]# grep docker  /etc/group
docker:x:992:ashu
[root@ip-172-31-31-88 ~]# 

```

### setting up image build environment 

```
[ashu@ip-172-31-31-88 ~]$ whoami
ashu
[ashu@ip-172-31-31-88 ~]$ docker  -v
Docker version 20.10.17, build 100c701
[ashu@ip-172-31-31-88 ~]$ ls
[ashu@ip-172-31-31-88 ~]$ mkdir ashu-images
[ashu@ip-172-31-31-88 ~]$ ls
ashu-images
[ashu@ip-172-31-31-88 ~]$ mkdir ashu-images/java
[ashu@ip-172-31-31-88 ~]$ mkdir ashu-images/python
[ashu@ip-172-31-31-88 ~]$ mkdir ashu-images/webapp
[ashu@ip-172-31-31-88 ~]$ ls
ashu-images
[ashu@ip-172-31-31-88 ~]$ ls  ashu-images/
java  python  webapp
[ashu@ip-172-31-31-88 ~]$ 

```

## Image building options 

<img src="op.png">

### Dockefile for image building purpose 

### python code -- ashu.py 

```
import time

while True:
    print("Hello all , welcome to python..!!")
    time.sleep(3)
    print("Welcome to Oracle India ..")
    time.sleep(2)
    print("Welcome to Containers By Docker..!!")
    print("______________________")
    time.sleep(3)
```

### Dockerfile

```
FROM python 
# we are targetting python docker image from docker hub registry 
LABEL name=ashutoshh
LABEL email=ashutoshh@linux.com 
# OPtional field but you can use to share image developer details to user
RUN mkdir /mycode 
# inside existing python image i am creating a directory 
# RUN you are asking for shell access 
COPY ashu.py /mycode/
# copy is an instruction to copy code into /mycode/
CMD ["python","/mycode/ashu.py"]
# CMD is to define process (single process) 
# when you create container from this image it will start this process
```

### lets build image

```
[ashu@ip-172-31-31-88 ashu-images]$ ls
java  python  webapp
[ashu@ip-172-31-31-88 ashu-images]$ docker build  -t  ashupy:v1     python/ 
Sending build context to Docker daemon  3.584kB
Step 1/6 : FROM python
latest: Pulling from library/python
b0248cf3e63c: Pull complete 
127e97b4daf7: Pull complete 
0336c50c9f69: Pull complete 
```

### checking image 

```
[ashu@ip-172-31-31-88 ashu-images]$ docker images
REPOSITORY   TAG       IMAGE ID       CREATED              SIZE
tejsh        v1        c787061f0590   About a minute ago   921MB
hemapy       v1        280b1df192f4   About a minute ago   921MB
naveenpy     v1        5e976659fa2c   About a minute ago   921MB
ishanpy      v1        52a47ec4b348   2 minutes ago     
```

### creating containers 

```
[ashu@ip-172-31-31-88 ashu-images]$ docker  run  -it  -d  --name ashuc1  ashupy:v1  
a4b08906be729aee12521e61608b27fd48971b35397ae807e319aaa58b4524f2
[ashu@ip-172-31-31-88 ashu-images]$ docker  ps
CONTAINER ID   IMAGE       COMMAND                  CREATED         STATUS         PORTS     NAMES
a4b08906be72   ashupy:v1   "python /mycode/ashu…"   3 seconds ago   Up 2 seconds             ashuc1
```

### checking output 

```
ashu@ip-172-31-31-88 ashu-images]$ docker logs  ashuc1 
======>
Hello all , welcome to python..!!
Welcome to Oracle India ..
Welcome to Containers By Docker..!!

```


