##

### Introduction of podman 

<img src="podman.png">

### Installing podman

```
[root@ip-172-31-93-201 ~]# dnf install podman 
Updating Subscription Management repositories.
Unable to read consumer identity

This system is not registered with an entitlement server. You can use subscription-manager to register.

Red Hat Enterprise Linux 9 for x86_64 - AppStream from RHUI (RPMs)                               20 MB/s |  18 MB     00:00    
Red Hat Enterprise Linux 9 for x86_64 - BaseOS from RHUI (RPMs)                                  17 MB/s |  10 MB     00:00    
Red Hat Enterprise Linux 9 Client Configuration                                                  23 kB/s | 2.8 kB     00:00    
Last metadata expiration check: 0:00:01 ago on Wed 19 Apr 2023 04:42:22 AM UTC.
Dependencies resolved.
================================================================================================================================
 Package                        Architecture       Version                         Repository                              Size
================================================================================================================================
Installing:
 podman                         x86_64             2:4.2.0-11.el9_1                rhel-9-appstream-rhui-rpms              12 M
Installing dependencies:
 conmon                         x86_64             2:2.1.4-1.el9                   rhel-9-appstream-rhui-rpms              55 k
 container-selinux              noarch             3:2.189.0-1.el9                 rhel-9-appstream-rhui-rpms              53 k
 containers-common              x86_64             2:1-49.el9_1                    rhel-9-appstream-rhui-rpms             121 k
 criu                           x86_64       
```

### configuration directory 

```
[root@ip-172-31-93-201 ~]# cd /etc/containers/
[root@ip-172-31-93-201 containers]# ls
certs.d  oci  policy.json  registries.conf  registries.conf.d  registries.d  storage.conf
[root@ip-172-31-93-201 containers]# 


```

### No service start required 

```
[root@ip-172-31-93-201 containers]# podman images
REPOSITORY  TAG         IMAGE ID    CREATED     SIZE
[root@ip-172-31-93-201 containers]# podman ps
CONTAINER ID  IMAGE       COMMAND     CREATED     STATUS      PORTS       NAMES
[root@ip-172-31-93-201 containers]# podman 

```

### giving podman access to a non root user 

```
[root@ip-172-31-93-201 ~]# useradd  oracle 
[root@ip-172-31-93-201 ~]# 
[root@ip-172-31-93-201 ~]# 
[root@ip-172-31-93-201 ~]# su - oracle
[oracle@ip-172-31-93-201 ~]$ 
[oracle@ip-172-31-93-201 ~]$ 
[oracle@ip-172-31-93-201 ~]$ id
uid=1001(oracle) gid=1001(oracle) groups=1001(oracle) context=unconfined_u:unconfined_r:unconfined_t:s0-s0:c0.c1023
[oracle@ip-172-31-93-201 ~]$ 
[oracle@ip-172-31-93-201 ~]$ podman images
WARN[0000] The cgroupv2 manager is set to systemd but there is no systemd user session available 
WARN[0000] For using systemd, you may need to login using an user session 
WARN[0000] Alternatively, you can enable lingering with: `loginctl enable-linger 1001` (possibly as root) 
WARN[0000] Falling back to --cgroup-manager=cgroupfs    
REPOSITORY  TAG         IMAGE ID    CREATED     SIZE
WARN[0000] Failed to add pause process to systemd sandbox cgroup: exec: "dbus-launch": executable file not found in $PATH 
[oracle@ip-172-31-93-201 ~]$ 
logout
[root@ip-172-31-93-201 ~]# loginctl enable-linger 1001
[root@ip-172-31-93-201 ~]# su - oracle
Last login: Wed Apr 19 04:44:35 UTC 2023 on pts/0
[oracle@ip-172-31-93-201 ~]$ podman images
REPOSITORY  TAG         IMAGE ID    CREATED     SIZE
[oracle@ip-172-31-93-201 ~]$ 


```


### podman can do auto search in multiple registry

```
[root@ip-172-31-93-201 ~]# cd  /etc/containers/
[root@ip-172-31-93-201 containers]# ls
certs.d  networks  oci  policy.json  registries.conf  registries.conf.d  registries.d  storage.conf
[root@ip-172-31-93-201 containers]# vim registries.conf
-bash: vim: command not found
[root@ip-172-31-93-201 containers]# dnf install vim -y &>/dev/null 
[root@ip-172-31-93-201 containers]# 
[root@ip-172-31-93-201 containers]# vim registries.conf
[root@ip-172-31-93-201 containers]# vim registries.conf
[root@ip-172-31-93-201 containers]# grep -in unqualified  registries.conf
3:# NOTE: RISK OF USING UNQUALIFIED IMAGE NAMES
20:# # An array of host[:port] registries to try when pulling an unqualified image, in order.
22:unqualified-search-registries = ["registry.access.redhat.com", "registry.redhat.io", "docker.io"]
[root@ip-172-31-93-201 containers]# 



```

### pulling images 

```
[root@ip-172-31-93-201 ~]# podman  pull dockerashu/ashucimg:v007
✔ docker.io/dockerashu/ashucimg:v007
Trying to pull docker.io/dockerashu/ashucimg:v007...
Getting image source signatures
Copying blob 7a015defac78 done  
Copying blob 7a015defac78 done  
Copying blob a4df6f21af84 done  
Copying config d4d0377464 done  
Writing manifest to image destination
Storing signatures
d4d0377464d5a4ab31307880f8d371c2101b45fcef31cb723a01b76b74e080aa
[root@ip-172-31-93-201 ~]# 
[root@ip-172-31-93-201 ~]# podman images
REPOSITORY                       TAG         IMAGE ID      CREATED       SIZE
docker.io/dockerashu/ashucimg    v007        d4d0377464d5  18 hours ago  537 MB
docker.io/library/alpine         latest      9ed4aefc74f6  2 weeks ago   7.34 MB
registry.access.redhat.com/ubi8  latest      768688a18971  3 weeks ago   214 MB
[root@ip-172-31-93-201 ~]# 
[root@ip-172-31-93-201 ~]# 
[root@ip-172-31-93-201 ~]# su - oracle
Last login: Wed Apr 19 04:45:27 UTC 2023 on pts/0
[oracle@ip-172-31-93-201 ~]$ 
[oracle@ip-172-31-93-201 ~]$ 
[oracle@ip-172-31-93-201 ~]$ podman images
REPOSITORY  TAG         IMAGE ID    CREATED     SIZE
[oracle@ip-172-31-93-201 ~]$ 


```

