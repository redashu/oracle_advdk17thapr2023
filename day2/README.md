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

