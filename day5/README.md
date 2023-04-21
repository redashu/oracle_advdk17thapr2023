##

### Docker inside a running container 

<img src="sock.png">

### docker web ui 

```
root@ip-172-31-31-88 ~]# docker run -itd --name monitor -p 9000:9000 -v /var/run/docker.sock:/var/run/docker.sock portainer/portainer 
Unable to find image 'portainer/portainer:latest' locally
latest: Pulling from portainer/portainer
772227786281: Pull complete 
96fd13befc87: Pull complete 
0bad1d247b5b: Pull complete 
b5d1b01b1d39: Pull complete 
Digest: sha256:47b064434edf437badf7337e516e07f64477485c8ecc663ddabbe824b20c672d
Status: Downloaded newer image for portainer/portainer:latest
52fecbc4b3718c3e253b3a84f2b56b177c6d231c375a5a38f3045eba632d0698
[root@ip-172-31-31-88 ~]# docker ps
CONTAINER ID   IMAGE                 COMMAND                CREATED         STATUS             PORTS                                                           NAMES
52fecbc4b371   portainer/portainer   "/portainer"           2 minutes ago   Up 2 minutes       8000/tcp, 9443/tcp, 0.0.0.0:9000->9000/tcp, :::9000->9000/tcp   monitor

```


### problem with Loadbalancer 

<img src="lb1.png">

### Ingress controller 

<img src="ingress.png">

