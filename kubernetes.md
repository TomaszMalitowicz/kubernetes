# How to kubernetes


### instalacja Mircok8s na ubuntu  
`sudo apt install snap`  
`snap info microk8s`  
`sudo snap install microk8s --classic --channel=1.17/stable` - instalacja wersji 1.17.  
`sudo usermod -a -G microk8s user` - dodanie uzytkowniak do grupy microk8s   


`microk8s.status`  
```
microk8s is running
addons:
cilium: disabled
dashboard: disabled
dns: disabled
fluentd: disabled
gpu: disabled
helm: disabled
ingress: disabled
istio: disabled
jaeger: disabled
juju: disabled
knative: disabled
kubeflow: disabled
linkerd: disabled
metallb: disabled
metrics-server: disabled
prometheus: disabled
rbac: disabled
registry: disabled
storage: disabled
```

`microk8s.kubectl`  - wyswietla liste polecenie.  
`sudo snap alias microk8s.kubectl kubectl`  
`sudo microk8s.kubectl config view --raw > $HOME/.kube/config`  

ok sprawdzmy wersje:  
`kubectl version`  
```
Client Version: version.Info{Major:"1", Minor:"17", GitVersion:"v1.17.5", GitCommit:"e0fccafd69541e3750d460ba0f9743b90336f24f", GitTreeState:"clean", BuildDate:"2020-04-16T11:44:03Z", GoVersion:"go1.13.9", Compiler:"gc", Platform:"linux/amd64"}
Server Version: version.Info{Major:"1", Minor:"17", GitVersion:"v1.17.5", GitCommit:"e0fccafd69541e3750d460ba0f9743b90336f24f", GitTreeState:"clean", BuildDate:"2020-04-16T11:35:47Z", GoVersion:"go1.13.9", Compiler:"gc", Platform:"linux/amd64"}
```

stworzmy pierwszego poda:  
`kubectl run my-nginx --image nginx`  

wystwietlmy liste podow:  
`kubectl get pods`  
```
NAME                        READY   STATUS    RESTARTS   AGE
my-nginx-669bb4594c-l752p   1/1     Running   0          48s
```

`kubectl delete deployment my-nginx`  
```
deployment.apps "my-nginx" deleted
```

### disclamer  
from version 1.18+ kubectl run command only does on thins: create single pods.  
in older version 1.17 and less kubectl run create a deployment which create a ReplicaSet, which creates a Pod.  
how to create deployment in 1.18+ ? kubectl create deployment nginx --image nginx  

### Scaling

`kubectl run my-apache --image httpd`  
`kubectl get all`  
```
NAME                             READY   STATUS              RESTARTS   AGE
pod/my-apache-5d589d69c7-rj8gt   0/1     ContainerCreating   0          3s

NAME                 TYPE        CLUSTER-IP     EXTERNAL-IP   PORT(S)   AGE
service/kubernetes   ClusterIP   10.152.183.1   <none>        443/TCP   76m

NAME                        READY   UP-TO-DATE   AVAILABLE   AGE
deployment.apps/my-apache   0/1     1            0           3s

NAME                                   DESIRED   CURRENT   READY   AGE
replicaset.apps/my-apache-5d589d69c7   1         1         0       3s
```
ok podniesmy liczbe podow do 2:  
`kubectl scale deploy/my-apache --replicas 2`  
`kubectl get all`  
```
NAME                             READY   STATUS    RESTARTS   AGE
pod/my-apache-5d589d69c7-lxmf6   1/1     Running   0          2m16s
pod/my-apache-5d589d69c7-rj8gt   1/1     Running   0          3m19s

NAME                 TYPE        CLUSTER-IP     EXTERNAL-IP   PORT(S)   AGE
service/kubernetes   ClusterIP   10.152.183.1   <none>        443/TCP   79m

NAME                        READY   UP-TO-DATE   AVAILABLE   AGE
deployment.apps/my-apache   2/2     2            2           3m19s

NAME                                   DESIRED   CURRENT   READY   AGE
replicaset.apps/my-apache-5d589d69c7   2         2         2       3m19s
```

### inspect/logs

`kubectl get pods`  
```
NAME                         READY   STATUS    RESTARTS   AGE
my-apache-5d589d69c7-lxmf6   1/1     Running   0          15m
my-apache-5d589d69c7-rj8gt   1/1     Running   0          17m
```
`kubectl logs deployment/my-apache`  
```
Found 2 pods, using pod/my-apache-5d589d69c7-rj8gt
AH00558: httpd: Could not reliably determine the server's fully qualified domain name, using 10.1.20.3. Set the 'ServerName' directive globally to suppress this message
AH00558: httpd: Could not reliably determine the server's fully qualified domain name, using 10.1.20.3. Set the 'ServerName' directive globally to suppress this message
[Sat May 23 17:01:07.545886 2020] [mpm_event:notice] [pid 1:tid 140174204232832] AH00489: Apache/2.4.43 (Unix) configured -- resuming normal operations
[Sat May 23 17:01:07.546050 2020] [core:notice] [pid 1:tid 140174204232832] AH00094: Command line: 'httpd -D FOREGROUND'
```
`kubectl logs deployment/my-apache --follow --tail 1`  

`kubectl logs -l run=my-apache`  

```
kubectl get pods
NAME                         READY   STATUS    RESTARTS   AGE
my-apache-5d589d69c7-lxmf6   1/1     Running   0          20m
my-apache-5d589d69c7-rj8gt   1/1     Running   0          21m
```

`kubectl describe pod/my-apache-5d589d69c7-lxmf6`  

`kubectl get pods -w` - wyswietla nam liste podow i na bierzaco ja aktualizuje.
```
NAME                         READY   STATUS    RESTARTS   AGE
my-apache-5d589d69c7-lxmf6   1/1     Running   0          25m
my-apache-5d589d69c7-rj8gt   1/1     Running   0          26m
```
w drugim oknie usunmy jednego poda.  
`kubectl delete pod/my-apache-5d589d69c7-lxmf6`  
pod "my-apache-5d589d69c7-lxmf6" deleted  

w tym czasie proces `kubectl get pods -w` zaktualizowal sie:  
```
NAME                         READY   STATUS    RESTARTS   AGE
my-apache-5d589d69c7-lxmf6   1/1     Running   0          25m
my-apache-5d589d69c7-rj8gt   1/1     Running   0          26m
my-apache-5d589d69c7-lxmf6   1/1     Terminating   0          26m
my-apache-5d589d69c7-gh87k   0/1     Pending       0          0s
my-apache-5d589d69c7-gh87k   0/1     Pending       0          0s
my-apache-5d589d69c7-gh87k   0/1     ContainerCreating   0          0s
my-apache-5d589d69c7-lxmf6   0/1     Terminating         0          26m
my-apache-5d589d69c7-gh87k   1/1     Running             0          2s
my-apache-5d589d69c7-lxmf6   0/1     Terminating         0          26m
my-apache-5d589d69c7-lxmf6   0/1     Terminating         0          26m
```

`kubectl get pods`  
```
NAME                         READY   STATUS    RESTARTS   AGE
my-apache-5d589d69c7-gh87k   1/1     Running   0          2m25s
my-apache-5d589d69c7-rj8gt   1/1     Running   0          29m
```
jak widac pod sie odtworzyl.  

czyscimy deploymenty:  
`kubectl delete deployment my-apache`  



### Services
ClusterIP  
NodePort  
LoadBalancer  

#### ClusterIP service

terminal1:  
`kubectl get pods -w`  

terminal2:  
`kubectl create deployment httpenv --image=bretfisher/httpenv`  

podnosimy liczbe podow do 5:  
`kubectl scale deployment/httpenv --replicas=5`  

utworzmy serwis:  
`kubectl expose deployment/httpenv --port 8888`   

`kubectl get service`  
```
NAME         TYPE        CLUSTER-IP       EXTERNAL-IP   PORT(S)    AGE
httpenv      ClusterIP   10.152.183.111   <none>        8888/TCP   10s
kubernetes   ClusterIP   10.152.183.1     <none>        443/TCP    140m
```

uzywamy curla do sprawdzenia czy servis dziala:
`curl 10.152.183.111:8888`  
```
{"HOME":"/root","HOSTNAME":"httpenv-6bf64f7c4f-bwpk2","KUBERNETES_PORT":"tcp://10.152.183.1:443","KUBERNETES_PORT_443_TCP":"tcp://10.152.183.1:443","KUBERNETES_PORT_443_TCP_ADDR":"10.152.183.1","KUBERNETES_PORT_443_TCP_PORT":"443","KUBERNETES_PORT_443_TCP_PROTO":"tcp","KUBERNETES_SERVICE_HOST":"10.152.183.1","KUBERNETES_SERVICE_PORT":"443","KUBERNETES_SERVICE_PORT_HTTPS":"443","PATH":"/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin"}
```

`kubectl run --generator run-pod/v1 tmp-shell --rm -it --image bretfisher/netshoot -- bash`  


#### NodePort

tworzymy node port do wczesniej utworzonego deploymentu  
`kubectl expose deployment/httpenv --port 8888`  

`kubectl get services`  
```
NAME         TYPE        CLUSTER-IP       EXTERNAL-IP   PORT(S)          AGE
httpenv      ClusterIP   10.152.183.111   <none>        8888/TCP         12m
httpenv-np   NodePort    10.152.183.35    <none>        8888:32126/TCP   80s
kubernetes   ClusterIP   10.152.183.1     <none>        443/TCP          152m
```

`curl 10.152.183.35:8888`  
```
{"HOME":"/root","HOSTNAME":"httpenv-6bf64f7c4f-tk2vd","KUBERNETES_PORT":"tcp://10.152.183.1:443","KUBERNETES_PORT_443_TCP":"tcp://10.152.183.1:443","KUBERNETES_PORT_443_TCP_ADDR":"10.152.183.1","KUBERNETES_PORT_443_TCP_PORT":"443","KUBERNETES_PORT_443_TCP_PROTO":"tcp","KUBERNETES_SERVICE_HOST":"10.152.183.1","KUBERNETES_SERVICE_PORT":"443","KUBERNETES_SERVICE_PORT_HTTPS":"443","PATH":"/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin"}
```
cleanup:
`kubectl delete service/httpenv service/httpenv-np`  
`kubectl delete deployment httpenv`  


### DNS

`kubectl get namespaces`  
```
NAME              STATUS   AGE
default           Active   174m
kube-node-lease   Active   174m
kube-public       Active   174m
kube-system       Active   174m
```



`kubectl create deployment sample --image nginx --dry-run -o yaml`  
`kubectl create job test --image nginx --dry-run -o yaml`  


`kubectl create deployment test --image nginx`  

`kubectl expose deployment/test --port 80 --dry-run -o yaml`  
`kubectl delete deployment test`  


### YAML
pod.yml
```yml
apiVersion:v1
kind: Pod
metadata:
  name: nginx
spec:
  containers:
  - name: nginx
    image: nginx:1.17.3
    ports:
    - containerPort: 80
``` 

jak nauczyc sie tworzyc yaml'a od zera ?
Podzielmy go na 4 sekcje:
apiVersion:
kind:
metadata: <!--- tylko nazwa
spec: <!--- tutaj jest duzo mozliwosci


`kubectl api-resources`    
`kubectl api-versions`  

`kubectl explain services --recursive`  
`kubectl explain services.spec`  
`kubectl explain services.spec.type`
`kubectl explain deployment.spec.template.spec.volumes.nfs.server`  

wersja webowa.
https://kubernetes.io/docs/reference/#api-reference


```yml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: nginx-deployment
spec:
  selector:
    matchLabels:
      app: nginx
  replicas: 2
  template:
    metadata:
      labels:
        app: nginx
    spec:
      containers:
      - name: nginx
        image: nginx:1.17.3
        ports:
        - containerPort: 80
```

```yml
apiVersion: v1
kind: Service
metadata:
  name: app-nginx-service
spec:
  type: NodePort
  ports:
  - port: 80
  selector:
    app: app-nginx
---
apiVersion: apps/v1
kind: Deployment
metadata:
  name: app-nginx-deployment
spec:
  replicas: 3
  selector:
    matchLabels:
      app: app-nginx
  template:
    metadata:
      labels:
        app: app-nginx
    spec:
      containers:
      - name: nginx
        image: nginx:1.17.3
        ports:
        - containerPort: 80
```