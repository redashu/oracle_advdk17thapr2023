## Revision of k8s 

<img src="rev.png">

### verify connection and namespace 

```
ashu@ip-172-31-31-88 ashu-images]$ kubectl  get  nodes
NAME      STATUS   ROLES           AGE   VERSION
master    Ready    control-plane   14h   v1.27.1
minion1   Ready    <none>          14h   v1.27.1
minion2   Ready    <none>          14h   v1.27.1
[ashu@ip-172-31-31-88 ashu-images]$ kubectl  get  ns
NAME               STATUS   AGE
ashu-deploy        Active   14h
default            Active   14h
hema-deploy        Active   14h
ishan-deploy       Active   14h
kube-node-lease    Active   14h
kube-public        Active   14h
kube-system        Active   14h
naveen-deploy-ns   Active   14h
ravinder-ns        Active   14h
saivisal-deploy    Active   14h
sat-namespace      Active   14h
t-ns               Active   14h
tasks              Active   14h
[ashu@ip-172-31-31-88 ashu-images]$ kubectl config set-context --current --namespace ashu-deploy 
Context "kubernetes-admin@kubernetes" modified.
[ashu@ip-172-31-31-88 ashu-images]$ kubectl config get-contexts 
CURRENT   NAME                          CLUSTER      AUTHINFO           NAMESPACE
*         kubernetes-admin@kubernetes   kubernetes   kubernetes-admin   ashu-deploy
[ashu@ip-172-31-31-88 ashu-images]$ 

```

### k8s internal component are deployed in kube-system namespace 

```
ashu@ip-172-31-31-88 ashu-images]$ kubectl  get po -n kube-system    
NAME                                       READY   STATUS    RESTARTS      AGE
calico-kube-controllers-6c99c8747f-8plm9   1/1     Running   1 (44m ago)   14h
calico-node-c49mv                          1/1     Running   1 (44m ago)   14h
calico-node-jbkdc                          1/1     Running   1 (44m ago)   14h
calico-node-sm79b                          1/1     Running   1 (44m ago)   14h
coredns-5d78c9869d-8xn62                   1/1     Running   1 (44m ago)   14h
coredns-5d78c9869d-gh6j2                   1/1     Running   1 (44m ago)   14h
etcd-master                                1/1     Running   1 (44m ago)   14h
kube-apiserver-master                      1/1     Running   1 (44m ago)   14h
kube-controller-manager-master             1/1     Running   1 (44m ago)   14h
kube-proxy-76xgz                           1/1     Running   1 (44m ago)   14h
kube-proxy-t766g                           1/1     Running   1 (44m ago)   14h
kube-proxy-xx4vb                           1/1     Running   1 (44m ago)   14h
kube-scheduler-master                      1/1     Running   1 (44m ago)   14h
```

## java spring app using multi stage dockerfile 

<img src="spr.png">

### taking sample spring boot app 

```
ashu@ip-172-31-31-88 ashu-images]$ mkdir  ashu-multistage
[ashu@ip-172-31-31-88 ashu-images]$ ls
ashu-customer  ashu-multistage  ashu-task  ashuwebapp  java  k8s-app-deploy  python
[ashu@ip-172-31-31-88 ashu-images]$ cd ashu-multistage/
[ashu@ip-172-31-31-88 ashu-multistage]$ ls
Dockerfile
[ashu@ip-172-31-31-88 ashu-multistage]$ git clone https://github.com/redashu/java-springboot.git
Cloning into 'java-springboot'...
remote: Enumerating objects: 23, done.
remote: Counting objects: 100% (23/23), done.
remote: Compressing objects: 100% (17/17), done.
remote: Total 23 (delta 4), reused 0 (delta 0), pack-reused 0
Receiving objects: 100% (23/23), 5.62 KiB | 1.12 MiB/s, done.
Resolving deltas: 100% (4/4), done.
[ashu@ip-172-31-31-88 ashu-multistage]$ ls
Dockerfile  java-springboot
[ashu@ip-172-31-31-88 ashu-multistage]$ 


```

### Dockerfile 

```
from oraclelinux:8.4  as Stage1 
LABEL name=ashutoshh
RUN yum install maven java-1.8.0-openjdk.x86_64 java-1.8.0-openjdk-devel.x86_64  -y 
RUN mkdir /myapp
ADD java-springboot  /myapp/
WORKDIR /myapp
RUN mvn clean package 
# above command will build a war file from java spring app 
# it will create target forlder and under that it will create WebApp.war file 
FROM tomcat 
LABEL name=ashutoshh
LABEL email=ashutoshh@linux.com 
COPY --from=Stage1  /myapp/target/WebApp.war /usr/local/tomcat/webapps/
# if we don't use cmd / entrypoint for process definition 
# then your FROM image default cmd / entrypoint will be considered
```

### lets build it 

```
[ashu@ip-172-31-31-88 ashu-multistage]$ ls
Dockerfile  java-springboot
[ashu@ip-172-31-31-88 ashu-multistage]$ docker build  -t  docker.io/dockerashu/oraclespring:ashuv1 . 
Sending build context to Docker daemon  15.36kB
Step 1/11 : from oraclelinux:8.4  as Stage1
 ---> 97e22ab49eea
Step 2/11 : LABEL name=ashutoshh
 ---> Running in 79a509d1e199
Removing intermediate container 79a509d1e199
 ---> e3009e2d1dbc
Step 3/11 : RUN yum install maven java-1.8.0-openjdk.x86_64 java-1.8.0-openjdk-devel.x86_64  -y
 ---> Running in a5d313b9fec9
Oracle Linux 8 BaseOS Latest (x86_64)            70 MB/s |  57 MB     00:00    

```

### checking image

```
[ashu@ip-172-31-31-88 ashu-multistage]$ docker images  | grep dockerashu
dockerashu/oraclespring              ashuv1    3c97cea4df5d   22 seconds ago       475MB
dockerashu/ashucimg                  v007      d4d0377464d5   42 hours ago         526MB
```

### pushing 

```
[ashu@ip-172-31-31-88 ashu-multistage]$ docker login 
Authenticating with existing credentials...
WARNING! Your password will be stored unencrypted in /home/ashu/.docker/config.json.
Configure a credential helper to remove this warning. See
https://docs.docker.com/engine/reference/commandline/login/#credentials-store

Login Succeeded
[ashu@ip-172-31-31-88 ashu-multistage]$ docker push  docker.io/dockerashu/oraclespring:ashuv1
The push refers to repository [docker.io/dockerashu/oraclespring]
f4bc6bcc2c0f: Pushed 
34fa941837a6: Mounted from library/tomcat 
51855650441a: Mounted from library/tomcat 
509ce21d6674: Mounted from library/tomcat 
```

### pushing registry to azure cloud registy 

```
[ashu@ip-172-31-31-88 ashu-multistage]$ docker tag  docker.io/dockerashu/oraclespring:ashuv1   ashutoshh.azurecr.io/oraclespring:ashuv1
[ashu@ip-172-31-31-88 ashu-multistage]$ 
[ashu@ip-172-31-31-88 ashu-multistage]$ 
[ashu@ip-172-31-31-88 ashu-multistage]$ docker  login  ashutoshh.azurecr.io  
Username: ashutoshh
Password: 
WARNING! Your password will be stored unencrypted in /home/ashu/.docker/config.json.
Configure a credential helper to remove this warning. See
https://docs.docker.com/engine/reference/commandline/login/#credentials-store

Login Succeeded
[ashu@ip-172-31-31-88 ashu-multistage]$ docker push  ashutoshh.azurecr.io/oraclespring:ashuv1
The push refers to repository [ashutoshh.azurecr.io/oraclespring]
f4bc6bcc2c0f: Pushed 
34fa941837a6: Pushed 
51855650441a: Pushed 
```

### PRoblem with POd based app deployment 

<img src="podp.png">

## Deployment controller in modern k8s apps 

<img src="deploy.png">

### api resources understading 

<img src="apis.png">

### listing 

```
[ashu@ip-172-31-31-88 ashu-multistage]$ kubectl  api-resources  
NAME                              SHORTNAMES   APIVERSION                             NAMESPACED   KIND
bindings                                       v1                                     true         Binding
componentstatuses                 cs           v1                                     false        ComponentStatus
configmaps                        cm           v1                                     true         ConfigMap
```

### creating deployment yaml 

```
kubectl create deployment  ashu-springapp --image=docker.io/dockerashu/oraclespring:ashuv1   --port 8080  --dry-run=client -o yaml  >deployment1.yaml
```

### yaml show

```
apiVersion: apps/v1
kind: Deployment
metadata:
  creationTimestamp: null
  labels:
    app: ashu-springapp
  name: ashu-springapp # name of deployment 
  namespace: ashu-deploy # optional step 
spec:
  replicas: 1 # min number of pod we require 
  selector:
    matchLabels:
      app: ashu-springapp
  strategy: {}
  template: # for pod info 
    metadata:
      creationTimestamp: null
      labels:
        app: ashu-springapp
    spec:
      containers:
      - image: docker.io/dockerashu/oraclespring:ashuv1
        name: oraclespring
        ports:
        - containerPort: 8080
        resources: {}
status: {}

```

### checking this 

```
ashu@ip-172-31-31-88 k8s-app-deploy]$ ls
app1.yaml  ashupod1.yaml  autopod.yaml  autopod1.json  deployment1.yaml  hello.yaml  task.yaml
[ashu@ip-172-31-31-88 k8s-app-deploy]$ kubectl  apply -f  deployment1.yaml 
deployment.apps/ashu-springapp created
[ashu@ip-172-31-31-88 k8s-app-deploy]$ kubectl  get  deploy 
NAME             READY   UP-TO-DATE   AVAILABLE   AGE
ashu-springapp   0/1     1            0           7s
[ashu@ip-172-31-31-88 k8s-app-deploy]$ kubectl  get  rs
NAME                        DESIRED   CURRENT   READY   AGE
ashu-springapp-67687989c9   1         1         1       13s
[ashu@ip-172-31-31-88 k8s-app-deploy]$ kubectl  get   po 
NAME                              READY   STATUS    RESTARTS   AGE
ashu-springapp-67687989c9-ln4b5   1/1     Running   0          17s
[ashu@ip-172-31-31-88 k8s-app-deploy]$ 

```

### self healing 

```
[ashu@ip-172-31-31-88 k8s-app-deploy]$ kubectl  get po  -o wide
NAME                              READY   STATUS    RESTARTS   AGE     IP             NODE      NOMINATED NODE   READINESS GATES
ashu-springapp-67687989c9-ln4b5   1/1     Running   0          2m51s   192.168.34.1   minion1   <none>           <none>
[ashu@ip-172-31-31-88 k8s-app-deploy]$ 
[ashu@ip-172-31-31-88 k8s-app-deploy]$ 
[ashu@ip-172-31-31-88 k8s-app-deploy]$ kubectl delete pod ashu-springapp-67687989c9-ln4b5 
pod "ashu-springapp-67687989c9-ln4b5" deleted
[ashu@ip-172-31-31-88 k8s-app-deploy]$ kubectl  get po  -o wide
NAME                              READY   STATUS    RESTARTS   AGE   IP             NODE      NOMINATED NODE   READINESS GATES
ashu-springapp-67687989c9-ksvdz   1/1     Running   0          4s    192.168.34.4   minion1   <none>           <none>
[ashu@ip-172-31-31-88 k8s-app-deploy]$ 

```

### scaling pod 

```
[ashu@ip-172-31-31-88 k8s-app-deploy]$ kubectl  get  deploy 
NAME             READY   UP-TO-DATE   AVAILABLE   AGE
ashu-springapp   1/1     1            1           3m49s
[ashu@ip-172-31-31-88 k8s-app-deploy]$ kubectl scale deployment  ashu-springapp  --replicas 3
deployment.apps/ashu-springapp scaled
[ashu@ip-172-31-31-88 k8s-app-deploy]$ kubectl  get po  -o wide
NAME                              READY   STATUS    RESTARTS   AGE   IP                NODE      NOMINATED NODE   READINESS GATES
ashu-springapp-67687989c9-fpgpk   1/1     Running   0          5s    192.168.179.198   minion2   <none>           <none>
ashu-springapp-67687989c9-ksvdz   1/1     Running   0          58s   192.168.34.4      minion1   <none>           <none>
ashu-springapp-67687989c9-stk7c   1/1     Running   0          5s    192.168.179.199   minion2   <none>           <none>
[ashu@ip-172-31-31-88 k8s-app-deploy]$ kubectl scale deployment  ashu-springapp  --replicas 1
deployment.apps/ashu-springapp scaled
[ashu@ip-172-31-31-88 k8s-app-deploy]$ kubectl  get po  -o wide
NAME                              READY   STATUS    RESTARTS   AGE   IP             NODE      NOMINATED NODE   READINESS GATES
ashu-springapp-67687989c9-ksvdz   1/1     Running   0          77s   192.168.34.4   minion1   <none>           <none>
[ashu@ip-172-31-31-88 k8s-app-deploy]$ 

```

## Networking 

<img src="calico.png">

### by default all the pods from any namespace are allowed to connect via CNI bridge

```
[ashu@ip-172-31-31-88 k8s-app-deploy]$ kubectl  run test --image=alpine --command sleep 1000 
pod/test created
[ashu@ip-172-31-31-88 k8s-app-deploy]$ kubectl  get  po 
NAME                              READY   STATUS    RESTARTS   AGE
ashu-springapp-67687989c9-ksvdz   1/1     Running   0          46m
test                              1/1     Running   0          3s
[ashu@ip-172-31-31-88 k8s-app-deploy]$ kubectl exec -it test -- sh 
/ # ping 192.168.179.201
PING 192.168.179.201 (192.168.179.201): 56 data bytes
64 bytes from 192.168.179.201: seq=0 ttl=63 time=0.201 ms
64 bytes from 192.168.179.201: seq=1 ttl=63 time=0.119 ms
64 bytes from 192.168.179.201: seq=2 ttl=63 time=0.113 ms
^C
--- 192.168.179.201 ping statistics ---
3 packets transmitted, 3 packets received, 0% packet loss
round-trip min/avg/max = 0.113/0.144/0.201 ms
/ # exit
[ashu@ip-172-31-31-88 k8s-app-deploy]$ 
```

### Internal Lb is a must thing in k8s networking 

<img src="lb11.png">

### Introduction to service --- for creating internal LB 
### 4 types of services we are having 

<img src="stype.png">

### creating Nodeport service 

```
ashu@ip-172-31-31-88 k8s-app-deploy]$ kubectl  get deploy 
NAME             READY   UP-TO-DATE   AVAILABLE   AGE
ashu-springapp   2/2     2            2           62m
[ashu@ip-172-31-31-88 k8s-app-deploy]$ 
[ashu@ip-172-31-31-88 k8s-app-deploy]$ kubectl  expose  deployment  ashu-springapp  --type NodePort --port 8080 --name ashulb --dry-run=client -o yaml  >nodeport1.yaml 
[ashu@ip-172-31-31-88 k8s-app-deploy]$ kubectl config get-contexts 
CURRENT   NAME                          CLUSTER      AUTHINFO           NAMESPACE
*         kubernetes-admin@kubernetes   kubernetes   kubernetes-admin   ashu-deploy
[ashu@ip-172-31-31-88 k8s-app-deploy]$ 

```

### Deploy svc 

```
[ashu@ip-172-31-31-88 k8s-app-deploy]$ kubectl get  svc 
No resources found in ashu-deploy namespace.
[ashu@ip-172-31-31-88 k8s-app-deploy]$ kubectl  apply -f  nodeport1.yaml 
service/ashulb created
[ashu@ip-172-31-31-88 k8s-app-deploy]$ 
[ashu@ip-172-31-31-88 k8s-app-deploy]$ kubectl get  svc 
NAME     TYPE       CLUSTER-IP      EXTERNAL-IP   PORT(S)          AGE
ashulb   NodePort   10.109.197.48   <none>        8080:32268/TCP   3s
[ashu@ip-172-31-31-88 k8s-app-deploy]$ 

```
### creating private image based pod 

```
[ashu@ip-172-31-31-88 k8s-app-deploy]$ kubectl run ashu-app --image=ashutoshh.azurecr.io/oraclespring:ashuv1 --port 8080 --dry-run=client -o yaml >privatepod.yaml 
[ashu@ip-172-31-31-88 k8s-app-deploy]$ 

```

### Deploy pod 

```
[ashu@ip-172-31-31-88 k8s-app-deploy]$ kubectl apply -f  privatepod.yaml 
pod/ashu-app created
[ashu@ip-172-31-31-88 k8s-app-deploy]$ kubectl  get  pods
NAME       READY   STATUS         RESTARTS   AGE
ashu-app   0/1     ErrImagePull   0          5s
[ashu@ip-172-31-31-88 k8s-app-deploy]$ 

```

### tracking error using evetns 

```
hu@ip-172-31-31-88 k8s-app-deploy]$ kubectl get events 
LAST SEEN   TYPE      REASON      OBJECT         MESSAGE
77s         Normal    Scheduled   pod/ashu-app   Successfully assigned ashu-deploy/ashu-app to minion2
39s         Normal    Pulling     pod/ashu-app   Pulling image "ashutoshh.azurecr.io/oraclespring:ashuv1"
38s         Warning   Failed      pod/ashu-app   Failed to pull image "ashutoshh.azurecr.io/oraclespring:ashuv1": rpc error: code = Unknown desc = failed to pull and unpack image "ashutoshh.azurecr.io/oraclespring:ashuv1": failed to resolve reference "ashutoshh.azurecr.io/oraclespring:ashuv1": failed to authorize: failed to fetch anonymous token: unexpected status: 401 Unauthorized
38s         Warning   Failed      pod/ashu-app   Error: ErrImagePull
10s         Normal    BackOff     pod/ashu-app   Back-off pulling image "ashutoshh.azurecr.io/oraclespring:ashuv1"
10s         Warning   Failed      pod/ashu-app   Error: ImagePullBackOff
```

## Introduction to secret in k8s 

### we are going to store registry credential 

### creating secret to store azure registry auth details 

```
[ashu@ip-172-31-31-88 k8s-app-deploy]$ kubectl create secret 
Create a secret using specified subcommand.

Available Commands:
  docker-registry   Create a secret for use with a Docker registry
  generic           Create a secret from a local file, directory, or literal value
  tls               Create a TLS secret

Usage:
```

### 

```
kubectl create secret docker-registry  ashu-img-sec --docker-server=ashutoshh.azurecr.io --docker-username=ashutoshh --docker-password="Q+wxV" --dry-run=client -o yaml >secret1.yaml 
```

### namespace orinted secret 

```
[ashu@ip-172-31-31-88 k8s-app-deploy]$ kubectl apply -f secret1.yaml 
secret/ashu-img-sec created
[ashu@ip-172-31-31-88 k8s-app-deploy]$ kubectl  get secrets 
NAME           TYPE                             DATA   AGE
ashu-img-sec   kubernetes.io/dockerconfigjson   1      4s
[ashu@ip-172-31-31-88 k8s-app-deploy]$ 

```

### creating pod 

```
apiVersion: v1
kind: Pod
metadata:
  creationTimestamp: null
  labels:
    run: ashu-app
  name: ashu-app
spec:
  imagePullSecrets: # calling secret from this namespace 
  - name: ashu-img-sec 
  containers:
  - image: ashutoshh.azurecr.io/oraclespring:ashuv1
    name: ashu-app
    ports:
    - containerPort: 8080
    resources: {}
  dnsPolicy: ClusterFirst
  restartPolicy: Always
status: {}

```

### deploy it 

```
[ashu@ip-172-31-31-88 k8s-app-deploy]$ kubectl replace -f privatepod.yaml --force 
pod "ashu-app" deleted
pod/ashu-app replaced
[ashu@ip-172-31-31-88 k8s-app-deploy]$ kubectl get  po 
NAME       READY   STATUS    RESTARTS   AGE
ashu-app   1/1     Running   0          5s
[ashu@ip-172-31-31-88 k8s-app-deploy]$ 
```
### Multi resource yaml 

### command to generate yaml 

```
[ashu@ip-172-31-31-88 k8s-app-deploy]$ kubectl create ns  ashu-k8s1 --dry-run=client -o yaml 
kubectl run ashu-pod1 --image=ubuntu --command sleep 10000 --dry-run=client -o yaml 
kubectl create  service nodeport ashusvc --tcp 1234:80 --dry-run=client -o yaml
```

```
apiVersion: v1
kind: Namespace
metadata:
  creationTimestamp: null
  name: ashu-k8s1
spec: {}
status: {}
---
apiVersion: v1
kind: Pod
metadata:
  creationTimestamp: null
  labels:
    run: ashu-pod1
  name: ashu-pod1
  namespace: ashu-k8s1 # adding this line 
spec:
  containers:
  - command:
    - sleep
    - "10000"
    image: ubuntu
    name: ashu-pod1
    resources: {}
  dnsPolicy: ClusterFirst
  restartPolicy: Always
status: {}

---
apiVersion: v1
kind: Service
metadata:
  creationTimestamp: null
  labels:
    app: ashusvc
  name: ashusvc
  namespace: ashu-k8s1 # using namespace 
spec:
  ports:
  - name: 1234-80
    port: 1234
    protocol: TCP
    targetPort: 80
    nodePort: 31110 # define port statically 
  selector:
    app: ashusvc
  type: NodePort
status:
  loadBalancer: {}
```

### Deploy this 

```
[ashu@ip-172-31-31-88 k8s-app-deploy]$ kubectl apply -f mytask.yaml 
namespace/ashu-k8s1 created
pod/ashu-pod1 created
service/ashusvc created
[ashu@ip-172-31-31-88 k8s-app-deploy]$ kubectl  get  po,svc -n ashu-k8s1 
NAME            READY   STATUS    RESTARTS   AGE
pod/ashu-pod1   1/1     Running   0          13s

NAME              TYPE       CLUSTER-IP      EXTERNAL-IP   PORT(S)          AGE
service/ashusvc   NodePort   10.102.85.229   <none>        1234:31110/TCP   13s
[ashu@ip-172-31-31-88 k8s-app-deploy]$ 


```

### Destroy 

```
hu@ip-172-31-31-88 k8s-app-deploy]$ 
[ashu@ip-172-31-31-88 k8s-app-deploy]$ kubectll delete   -f mytask.yaml 
namespace "ashu-k8s1" deleted
pod "ashu-pod1" deleted
service "ashusvc" deleted


```
### more k8s client options 
<img src="k8sclient.png">

### Deploy k8s webui

```
ashu@ip-172-31-31-88 ashu-images]$ kubectl apply -f https://raw.githubusercontent.com/kubernetes/dashboard/v2.7.0/aio/deploy/recommended.yaml
namespace/kubernetes-dashboard created
serviceaccount/kubernetes-dashboard created
service/kubernetes-dashboard created
secret/kubernetes-dashboard-certs created
secret/kubernetes-dashboard-csrf created
secret/kubernetes-dashboard-key-holder created
configmap/kubernetes-dashboard-settings created
role.rbac.authorization.k8s.io/kubernetes-dashboard created
clusterrole.rbac.authorization.k8s.io/kubernetes-dashboard created
rolebinding.rbac.authorization.k8s.io/kubernetes-dashboard created
clusterrolebinding.rbac.authorization.k8s.io/kubernetes-dashboard created
deployment.apps/kubernetes-dashboard created
service/dashboard-metrics-scraper created
deployment.apps/dashboard-metrics-scraper created
```

### verify 

```
[ashu@ip-172-31-31-88 ashu-images]$ kubectl get  deploy -n kubernetes-dashboard 
NAME                        READY   UP-TO-DATE   AVAILABLE   AGE
dashboard-metrics-scraper   1/1     1            1           40s
kubernetes-dashboard        1/1     1            1           40s
[ashu@ip-172-31-31-88 ashu-images]$ kubectl get  svc  -n kubernetes-dashboard 
NAME                        TYPE        CLUSTER-IP      EXTERNAL-IP   PORT(S)    AGE
dashboard-metrics-scraper   ClusterIP   10.98.185.156   <none>        8000/TCP   48s
kubernetes-dashboard        ClusterIP   10.98.130.169   <none>        443/TCP    48s
[ashu@ip-172-31-31-88 ashu-images]$ kubectl get  secret  -n kubernetes-dashboard 
NAME                              TYPE     DATA   AGE
kubernetes-dashboard-certs        Opaque   0      58s
kubernetes-dashboard-csrf         Opaque   1      58s
kubernetes-dashboard-key-holder   Opaque   2      58s
```

### changing svc type to NodePort 

```
[ashu@ip-172-31-31-88 ashu-images]$ kubectl get  svc  -n kubernetes-dashboard 
NAME                        TYPE        CLUSTER-IP      EXTERNAL-IP   PORT(S)    AGE
dashboard-metrics-scraper   ClusterIP   10.98.185.156   <none>        8000/TCP   91s
kubernetes-dashboard        ClusterIP   10.98.130.169   <none>        443/TCP    91s
[ashu@ip-172-31-31-88 ashu-images]$ 
[ashu@ip-172-31-31-88 ashu-images]$ kubectl edit  svc   kubernetes-dashboard  -n kubernetes-dashboard 
service/kubernetes-dashboard edited
[ashu@ip-172-31-31-88 ashu-images]$ kubectl get  svc  -n kubernetes-dashboard 
NAME                        TYPE        CLUSTER-IP      EXTERNAL-IP   PORT(S)         AGE
dashboard-metrics-scraper   ClusterIP   10.98.185.156   <none>        8000/TCP        2m19s
kubernetes-dashboard        NodePort    10.98.130.169   <none>        443:30721/TCP   2m19s
[ashu@ip-172-31-31-88 ashu-images]$ 

```
### generating login token of dashboard 

```
[ashu@ip-172-31-31-88 ashu-images]$ kubectl get  serviceaccounts  -n kubernetes-dashboard 
NAME                   SECRETS   AGE
default                0         4m39s
kubernetes-dashboard   0         4m39s
[ashu@ip-172-31-31-88 ashu-images]$ kubectl create token kubernetes-dashboard   -n kubernetes-dashboard 
eyJhbGciOiJSUzI1NiIsImtpZCI6ImhhU05jUE81V2dBNHNkaXpvU1QxTGVzNGxEeE9GY05KSTVLQTdoMWt1SzQifQ.eyJhdWQiOlsiaHR0cHM6Ly9rdWJlcm5ldGVzLmRlZmF1bHQuc3ZjLmNsdXN0ZXIubG9jYWwiXSwiZXhwIjoxNjgxOTg4MDc4LCJpYXQiOjE2ODE5ODQ0NzgsImlzcyI6Imh0dHBzOi8va3ViZXJuZXRlcy5kZWZhdWx0LnN2Yy5jbHVzdGVyLmxvY2FsIiwia3ViZXJuZXRlcy5pbyI6eyJuYW1lc3BhY2UiOiJrdWJlcm5ldGVzLWRhc2hib2FyZCIsInNlcnZpY2VhY2NvdW50Ijp7Im5hbWUiOiJrdWJlcm5ldGVzLWRhc2hib2FyZCIsInVpZCI6Ijg4M2I4Y2Y4LWYyNDgtNGM5My05ODVkLTg4MThiYTc0MzU4NyJ9fSwibmJmIjoxNjgxOTg0NDc4LCJzdWIiOiJzeXN0ZW06c2VydmljZWFj
```

### granting permission to dashboard 

```
[ashu@ip-172-31-31-88 ashu-images]$ 
[ashu@ip-172-31-31-88 ashu-images]$ kubectl get  serviceaccounts  -n kubernetes-dashboard 
NAME                   SECRETS   AGE
default                0         7m37s
kubernetes-dashboard   0         7m37s
[ashu@ip-172-31-31-88 ashu-images]$ 
[ashu@ip-172-31-31-88 ashu-images]$ 
[ashu@ip-172-31-31-88 ashu-images]$ kubectl -n kubernetes-dashboard  create clusterrolebinding  xyz --clusterrole cluster-admin  --serviceaccount=kubernetes-dashboard:kubernetes-dashboard 
clusterrolebinding.rbac.authorization.k8s.io/xyz created
[ashu@ip-172-31-31-88 ashu-images]$ 



```

## Introduction to HPA 

<img src="hpa.png">

### k8s need monitoring of resource capability to scale pods 

<img src="mon.png">

### checking monitoring capability of node / pod 

```
ashu@ip-172-31-31-88 ashu-images]$ kubectl  top
Display Resource (CPU/Memory) usage.

 The top command allows you to see the resource consumption for nodes or pods.

 This command requires Metrics Server to be correctly configured and working on the server.

Available Commands:
  node          Display resource (CPU/memory) usage of nodes
  pod           Display resource (CPU/memory) usage of pods

Usage:
  kubectl top [flags] [options]

Use "kubectl <command> --help" for more information about a given command.
Use "kubectl options" for a list of global command-line options (applies to all commands).
[ashu@ip-172-31-31-88 ashu-images]$ kubectl   get  node
NAME      STATUS   ROLES           AGE   VERSION
master    Ready    control-plane   20h   v1.27.1
minion1   Ready    <none>          20h   v1.27.1
minion2   Ready    <none>          20h   v1.27.1
[ashu@ip-172-31-31-88 ashu-images]$ kubectl  top  node minion1 
error: Metrics API not available
[ashu@ip-172-31-31-88 ashu-images]$ kubectl get po 
NAME       READY   STATUS    RESTARTS   AGE
ashu-app   1/1     Running   0          116m
[ashu@ip-172-31-31-88 ashu-images]$ kubectl top pod ashu-app 
error: Metrics API not available
```

### Deploy metric server in k8s 

```
ashu@ip-172-31-31-88 ashu-images]$ kubectl apply -f https://raw.githubusercontent.com/redashu/k8s/hpa/hpa/components.yaml
serviceaccount/metrics-server created
clusterrole.rbac.authorization.k8s.io/system:aggregated-metrics-reader created
clusterrole.rbac.authorization.k8s.io/system:metrics-server created
rolebinding.rbac.authorization.k8s.io/metrics-server-auth-reader created
clusterrolebinding.rbac.authorization.k8s.io/metrics-server:system:auth-delegator created
clusterrolebinding.rbac.authorization.k8s.io/system:metrics-server created
service/metrics-server created
deployment.apps/metrics-server created
apiservice.apiregistration.k8s.io/v1beta1.metrics.k8s.io created
[ashu@ip-172-31-31-88 ashu-images]$ 
[ashu@ip-172-31-31-88 ashu-images]$ kubectl  get  po -n kube-system 
NAME                                       READY   STATUS    RESTARTS        AGE
calico-kube-controllers-6c99c8747f-8plm9   1/1     Running   1 (7h13m ago)   20h
calico-node-c49mv                          1/1     Running   1 (7h13m ago)   20h
calico-node-jbkdc                          1/1     Running   1 (7h12m ago)   20h
calico-node-sm79b                          1/1     Running   1 (7h13m ago)   20h
coredns-5d78c9869d-8xn62                   1/1     Running   1 (7h13m ago)   20h
coredns-5d78c9869d-gh6j2                   1/1     Running   1 (7h13m ago)   20h
etcd-master                                1/1     Running   1 (7h13m ago)   20h
kube-apiserver-master                      1/1     Running   0               2m41s
kube-controller-manager-master             1/1     Running   2 (3m10s ago)   20h
kube-proxy-76xgz                           1/1     Running   1 (7h12m ago)   20h
kube-proxy-t766g                           1/1     Running   1 (7h13m ago)   20h
kube-proxy-xx4vb                           1/1     Running   1 (7h13m ago)   20h
kube-scheduler-master                      1/1     Running   2 (3m10s ago)   20h
metrics-server-6df6656494-wv2qp            1/1     Running   0               11s
```

### testing 

```
[ashu@ip-172-31-31-88 ashu-images]$  kubectl  top  node minion1 
NAME      CPU(cores)   CPU%   MEMORY(bytes)   MEMORY%   
minion1   82m          4%     1027Mi          13%       
[ashu@ip-172-31-31-88 ashu-images]$  kubectl  top  node minion2
NAME      CPU(cores)   CPU%   MEMORY(bytes)   MEMORY%   
minion2   75m          3%     1132Mi          14%       
[ashu@ip-172-31-31-88 ashu-images]$ kubectl top pod  ashu-app 
NAME       CPU(cores)   MEMORY(bytes)   
ashu-app   1m           82Mi            
[ashu@ip-172-31-31-88 ashu-images]$ 

```

### showing hpa case 

```
[ashu@ip-172-31-31-88 k8s-app-deploy]$ kubectl create deployment ashu-ui --image=adminer --port 8080 --dry-run=client -o yaml >hpa1.yaml
[ashu@ip-172-31-31-88 k8s-app-deploy]$ 

```

### final YAML 

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
  template: # for creating pods 
    metadata:
      creationTimestamp: null
      labels:
        app: ashu-ui
    spec:
      containers:
      - image: adminer
        name: adminer
        ports:
        - containerPort: 8080
        resources:
          requests:
            memory: 100M
            cpu: 50m 
          limits:
            memory: 400M 
            cpu: 200m # 1 vcpu = 1000m (milicore)
status: {}

```

### deploy hpa 

```
[ashu@ip-172-31-31-88 k8s-app-deploy]$ kubectl  get  deploy 
NAME      READY   UP-TO-DATE   AVAILABLE   AGE
ashu-ui   1/1     1            1           5m32s
[ashu@ip-172-31-31-88 k8s-app-deploy]$ kubectl autoscale  deployment ashu-ui --cpu-percent 70 --max 20 --min 2 --dry-run=client -o yaml >autoscale.yaml 
[ashu@ip-172-31-31-88 k8s-app-deploy]$ kubectl apply -f autoscale.yaml 
horizontalpodautoscaler.autoscaling/ashu-ui created
[ashu@ip-172-31-31-88 k8s-app-deploy]$ kubectl get hpa
NAME      REFERENCE            TARGETS         MINPODS   MAXPODS   REPLICAS   AGE
ashu-ui   Deployment/ashu-ui   <unknown>/70%   2         20        0          4s
[ashu@ip-172-31-31-88 k8s-app-deploy]$ 

```

### checking 

```
[ashu@ip-172-31-31-88 k8s-app-deploy]$ kubectl apply -f autoscale.yaml 
horizontalpodautoscaler.autoscaling/ashu-ui created
[ashu@ip-172-31-31-88 k8s-app-deploy]$ kubectl get hpa
NAME      REFERENCE            TARGETS         MINPODS   MAXPODS   REPLICAS   AGE
ashu-ui   Deployment/ashu-ui   <unknown>/70%   2         20        0          4s
[ashu@ip-172-31-31-88 k8s-app-deploy]$ kubectl get hpa
NAME      REFERENCE            TARGETS   MINPODS   MAXPODS   REPLICAS   AGE
ashu-ui   Deployment/ashu-ui   2%/70%    2         20        2          44s
[ashu@ip-172-31-31-88 k8s-app-deploy]$ kubectl  get  deploy 
NAME      READY   UP-TO-DATE   AVAILABLE   AGE
ashu-ui   2/2     2            2           8m49s
[ashu@ip-172-31-31-88 k8s-app-deploy]$ 
```

### helm check

```
ashu@ip-172-31-31-88 ~]$ helm version 
WARNING: Kubernetes configuration file is group-readable. This is insecure. Location: /home/ashu/.kube/config
WARNING: Kubernetes configuration file is world-readable. This is insecure. Location: /home/ashu/.kube/config
version.BuildInfo{Version:"v3.11.3", GitCommit:"323249351482b3bbfc9f5004f65d400aa70f9ae7", GitTreeState:"clean", GoVersion:"go1.20.3"}
[ashu@ip-172-31-31-88 ~]$ 
[ashu@ip-172-31-31-88 ~]$ helm version 
WARNING: Kubernetes configuration file is group-readable. This is insecure. Location: /home/ashu/.kube/config
WARNING: Kubernetes configuration file is world-readable. This is insecure. Location: /home/ashu/.kube/config
version.BuildInfo{Version:"v3.11.3", GitCommit:"323249351482b3bbfc9f5004f65d400aa70f9ae7", GitTreeState:"clean", GoVersion:"go1.20.3"}
[ashu@ip-172-31-31-88 ~]$ chmod 400 ~/.kube/config 
[ashu@ip-172-31-31-88 ~]$ 
[ashu@ip-172-31-31-88 ~]$ helm version 
version.BuildInfo{Version:"v3.11.3", GitCommit:"323249351482b3bbfc9f5004f65d400aa70f9ae7", GitTreeState:"clean", GoVersion:"go1.20.3"}
[ashu@ip-172-31-31-88 ~]$ 

```

### adding repo 

```
[ashu@ip-172-31-31-88 ~]$ helm repo list
Error: no repositories to show
[ashu@ip-172-31-31-88 ~]$ helm repo add ashu-repo https://charts.bitnami.com/bitnami
"ashu-repo" has been added to your repositories
[ashu@ip-172-31-31-88 ~]$ 
[ashu@ip-172-31-31-88 ~]$ helm repo list
NAME            URL                               
ashu-repo       https://charts.bitnami.com/bitnami
[ashu@ip-172-31-31-88 ~]$ 

```

### deploy sample app 

```
ashu@ip-172-31-31-88 ~]$ helm search repo nginx 
NAME                                    CHART VERSION   APP VERSION     DESCRIPTION                                       
ashu-repo/nginx                         13.2.34         1.23.4          NGINX Open Source is a web server that can be a...
ashu-repo/nginx-ingress-controller      9.5.1           1.7.0           NGINX Ingress Controller is an Ingress controll...
ashu-repo/nginx-intel                   2.1.15          0.4.9           DEPRECATED NGINX Open Source for Intel is a lig...
[ashu@ip-172-31-31-88 ~]$ 
[ashu@ip-172-31-31-88 ~]$ helm install ashu-deployment  ashu-repo/nginx  
NAME: ashu-deployment
LAST DEPLOYED: Fri Apr 21 11:09:38 2023
NAMESPACE: ashu-deploy
STATUS: deployed
REVISION: 1
TEST SUITE: None
```

### 

```
[ashu@ip-172-31-31-88 ~]$ helm ls
NAME            NAMESPACE       REVISION        UPDATED                                 STATUS          CHART           APP VERSION
ashu-deployment ashu-deploy     1               2023-04-21 11:09:38.62727675 +0000 UTC  deployed        nginx-13.2.34   1.23.4     
[ashu@ip-172-31-31-88 ~]$ 
```

### verify 

```
p-172-31-31-88 ~]$ 
[ashu@ip-172-31-31-88 ~]$ kubectl get deployment 
NAME                    READY   UP-TO-DATE   AVAILABLE   AGE
ashu-deployment-nginx   1/1     1            1           66s
[ashu@ip-172-31-31-88 ~]$ kubectl  get  rs
NAME                               DESIRED   CURRENT   READY   AGE
ashu-deployment-nginx-5679f9b6c9   1         1         1       75s
[ashu@ip-172-31-31-88 ~]$ kubectl  get  po
NAME                                     READY   STATUS    RESTARTS   AGE
ashu-deployment-nginx-5679f9b6c9-4d64d   1/1     Running   0          78s
[ashu@ip-172-31-31-88 ~]$ kubectl  get  svc
NAME                    TYPE           CLUSTER-IP      EXTERNAL-IP   PORT(S)        AGE
ashu-deployment-nginx   LoadBalancer   10.96.251.141   <pending>     80:32763/TCP   80s
[ashu@ip-172-31-31-88 ~]$ 

```

### 

```
[ashu@ip-172-31-31-88 ~]$ helm ls
NAME            NAMESPACE       REVISION        UPDATED                                 STATUS          CHART           APP VERSION
ashu-deployment ashu-deploy     1               2023-04-21 11:09:38.62727675 +0000 UTC  deployed        nginx-13.2.34   1.23.4     
[ashu@ip-172-31-31-88 ~]$ 
[ashu@ip-172-31-31-88 ~]$ helm uninstall ashu-deployment 
release "ashu-deployment" uninstalled
[ashu@ip-172-31-31-88 ~]$ 
[ashu@ip-172-31-31-88 ~]$ 
[ashu@ip-172-31-31-88 ~]$ helm ls
NAME    NAMESPACE       REVISION        UPDATED STATUS  CHART   APP VERSION
[ashu@ip-172-31-31-88 ~]$ 
```

#### creating helm chart

```
[ashu@ip-172-31-31-88 ingress-using-deployment]$ helm create  ashu-charts
Creating ashu-charts
[ashu@ip-172-31-31-88 ingress-using-deployment]$ ls
ashu-charts  ashucm.yaml  deployment.yaml  ingress.yaml  svc.yaml
[ashu@ip-172-31-31-88 ingress-using-deployment]$ 
[ashu@ip-172-31-31-88 ingress-using-deployment]$ 
[ashu@ip-172-31-31-88 ingress-using-deployment]$ cd ashu-charts/
[ashu@ip-172-31-31-88 ashu-charts]$ ls
Chart.yaml  charts  templates  values.yaml
[ashu@ip-172-31-31-88 ashu-charts]$ cd  templates/
[ashu@ip-172-31-31-88 templates]$ ls
NOTES.txt  _helpers.tpl  deployment.yaml  hpa.yaml  ingress.yaml  service.yaml  serviceaccount.yaml  tests
[ashu@ip-172-31-31-88 templates]$ rm -rf *.yaml 
[ashu@ip-172-31-31-88 templates]$ ls
NOTES.txt  _helpers.tpl  tests
[ashu@ip-172-31-31-88 templates]$ rm -rf tests/
[ashu@ip-172-31-31-88 templates]$ ls
NOTES.txt  _helpers.tpl
[ashu@ip-172-31-31-88 templates]$ rm NOTES.txt 
[ashu@ip-172-31-31-88 templates]$ 
```



