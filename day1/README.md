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



