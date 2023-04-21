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

## Deploy application and explose using ingress controller 

```
[ashu@ip-172-31-31-88 ingress-using-deployment]$ kubectl  create  deployment ashu-ui --image=dockerashu/ashu-customer:v1  --port 80 --dry-run=client -o yaml >deployment.yaml 
[ashu@ip-172-31-31-88 ingress-using-deployment]$ ls
deployment.yaml
```

### using configMap to store variabels and values 

```
[ashu@ip-172-31-31-88 ingress-using-deployment]$ kubectl create configmap ashu-app-vars --from-
--from-env-file  --from-file      --from-literal   
[ashu@ip-172-31-31-88 ingress-using-deployment]$ kubectl create configmap ashu-app-vars --from-literal  app1=hello --from-literal app2=world --dry-run=client -o yaml >ashucm.yaml 
[ashu@ip-172-31-31-88 ingress-using-deployment]$ kubectl apply -f ashucm.yaml 
configmap/ashu-app-vars created
[ashu@ip-172-31-31-88 ingress-using-deployment]$ kubectl  get  cm 
NAME               DATA   AGE
ashu-app-vars      2      4s
kube-root-ca.crt   1      38h
[ashu@ip-172-31-31-88 ingress-using-deployment]$ 


```

### using configmap just to store variable and then callinside deployment file 

```
apiVersion: apps/v1
kind: Deployment
metadata:
  creationTimestamp: null
  labels:
    app: ashu-ui
  name: ashu-ui
spec:
  replicas: 1
  selector:
    matchLabels:
      app: ashu-ui
  strategy: {}
  template: # pod template section 
    metadata:
      creationTimestamp: null
      labels:
        app: ashu-ui
    spec:
      containers:
      - image: dockerashu/ashu-customer:v1
        name: ashu-customer
        ports:
        - containerPort: 80
        env: # calling / creat env 
        - name: deploy # name of orginal variable 
          valueFrom:
            configMapKeyRef: # calling configmap 
              name: ashu-app-vars
              key: app1 
        resources: {}
status: {}

```

### Deploy 

```
[ashu@ip-172-31-31-88 ingress-using-deployment]$ ls
ashucm.yaml  deployment.yaml
[ashu@ip-172-31-31-88 ingress-using-deployment]$ kubectl apply -f deployment.yaml 
deployment.apps/ashu-ui created
[ashu@ip-172-31-31-88 ingress-using-deployment]$ kubectl  get deploy 
NAME      READY   UP-TO-DATE   AVAILABLE   AGE
ashu-ui   0/1     1            0           4s
[ashu@ip-172-31-31-88 ingress-using-deployment]$ kubectl  get po
NAME                       READY   STATUS              RESTARTS   AGE
ashu-ui-5b758b774f-9r2wv   0/1     ContainerCreating   0          9s
[ashu@ip-172-31-31-88 ingress-using-deployment]$ kubectl  get po
NAME                       READY   STATUS    RESTARTS   AGE
ashu-ui-5b758b774f-9r2wv   1/1     Running   0          11s
[ashu@ip-172-31-31-88 ingress-using-deployment]$ 

```


