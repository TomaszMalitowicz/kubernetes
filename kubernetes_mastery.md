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
Client Version: version.Info{Major:"1", Minor:"18+", GitVersion:"v1.18.4-1+6f17be3f1fd54a", GitCommit:"6f17be3f1fd54a88681869d1cf8bedd5a2174504", GitTreeState:"clean", BuildDate:"2020-06-23T21:16:24Z", GoVersion:"go1.14.4", Compiler:"gc", Platform:"linux/amd64"}
Server Version: version.Info{Major:"1", Minor:"18+", GitVersion:"v1.18.4-1+6f17be3f1fd54a", GitCommit:"6f17be3f1fd54a88681869d1cf8bedd5a2174504", GitTreeState:"clean", BuildDate:"2020-06-23T21:17:52Z", GoVersion:"go1.14.4", Compiler:"gc", Platform:"linux/amd64"}
```

### Displaying Resources

`kubectl get nodes -o json | jq ".items[] | {name:.metadata.name} + .status.capacity"`  
```json
{
  "name": "ubuntuserver20microk8s",
  "cpu": "2",
  "ephemeral-storage": "19475088Ki",
  "hugepages-2Mi": "0",
  "memory": "8153284Ki",
  "pods": "110"
}
```
```
kubectl get nodes
NAME                     STATUS   ROLES    AGE    VERSION
ubuntuserver20microk8s   Ready    <none>   153m   v1.18.4-1+6f17be3f1fd54a
```

`kubectl describe node/ubuntuserver20microk8s`  
```
Name:               ubuntuserver20microk8s
Roles:              <none>
Labels:             beta.kubernetes.io/arch=amd64
                    beta.kubernetes.io/os=linux
                    kubernetes.io/arch=amd64
                    kubernetes.io/hostname=ubuntuserver20microk8s
                    kubernetes.io/os=linux
                    microk8s.io/cluster=true
Annotations:        node.alpha.kubernetes.io/ttl: 0
                    volumes.kubernetes.io/controller-managed-attach-detach: true
CreationTimestamp:  Thu, 09 Jul 2020 10:19:27 +0000
Taints:             <none>
Unschedulable:      false
Lease:
  HolderIdentity:  ubuntuserver20microk8s
  AcquireTime:     <unset>
  RenewTime:       Thu, 09 Jul 2020 12:53:44 +0000
Conditions:
  Type             Status  LastHeartbeatTime                 LastTransitionTime                Reason                       Message
  ----             ------  -----------------                 ------------------                ------                       -------
  MemoryPressure   False   Thu, 09 Jul 2020 12:52:23 +0000   Thu, 09 Jul 2020 10:19:27 +0000   KubeletHasSufficientMemory   kubelet has sufficient memory available
  DiskPressure     False   Thu, 09 Jul 2020 12:52:23 +0000   Thu, 09 Jul 2020 10:19:27 +0000   KubeletHasNoDiskPressure     kubelet has no disk pressure
  PIDPressure      False   Thu, 09 Jul 2020 12:52:23 +0000   Thu, 09 Jul 2020 10:19:27 +0000   KubeletHasSufficientPID      kubelet has sufficient PID available
  Ready            True    Thu, 09 Jul 2020 12:52:23 +0000   Thu, 09 Jul 2020 10:19:37 +0000   KubeletReady                 kubelet is posting ready status. AppArmor enabled
Addresses:
  InternalIP:  192.168.100.10
  Hostname:    ubuntuserver20microk8s
Capacity:
  cpu:                2
  ephemeral-storage:  19475088Ki
  hugepages-2Mi:      0
  memory:             8153284Ki
  pods:               110
Allocatable:
  cpu:                2
  ephemeral-storage:  18426512Ki
  hugepages-2Mi:      0
  memory:             8050884Ki
  pods:               110
System Info:
  Machine ID:                 081171531d77424c872b0bbfbfdf600a
  System UUID:                23c5ef03-9a3c-b341-81d2-d3fcfa6ab4ca
  Boot ID:                    ea5f6c69-b1b9-4650-a819-a83e75ef71bc
  Kernel Version:             5.4.0-40-generic
  OS Image:                   Ubuntu 20.04 LTS
  Operating System:           linux
  Architecture:               amd64
  Container Runtime Version:  containerd://1.3.4
  Kubelet Version:            v1.18.4-1+6f17be3f1fd54a
  Kube-Proxy Version:         v1.18.4-1+6f17be3f1fd54a
Non-terminated Pods:          (0 in total)
  Namespace                   Name    CPU Requests  CPU Limits  Memory Requests  Memory Limits  AGE
  ---------                   ----    ------------  ----------  ---------------  -------------  ---
Allocated resources:
  (Total limits may be over 100 percent, i.e., overcommitted.)
  Resource           Requests  Limits
  --------           --------  ------
  cpu                0 (0%)    0 (0%)
  memory             0 (0%)    0 (0%)
  ephemeral-storage  0 (0%)    0 (0%)
  hugepages-2Mi      0 (0%)    0 (0%)
Events:
  Type     Reason                   Age   From                                Message
  ----     ------                   ----  ----                                -------
  Normal   Starting                 71m   kube-proxy, ubuntuserver20microk8s  Starting kube-proxy.
  Normal   Starting                 71m   kubelet, ubuntuserver20microk8s     Starting kubelet.
  Warning  InvalidDiskCapacity      71m   kubelet, ubuntuserver20microk8s     invalid capacity 0 on image filesystem
  Normal   NodeAllocatableEnforced  71m   kubelet, ubuntuserver20microk8s     Updated Node Allocatable limit across pods
  Normal   NodeHasSufficientMemory  71m   kubelet, ubuntuserver20microk8s     Node ubuntuserver20microk8s status is now: NodeHasSufficientMemory
  Normal   NodeHasNoDiskPressure    71m   kubelet, ubuntuserver20microk8s     Node ubuntuserver20microk8s status is now: NodeHasNoDiskPressure
  Normal   NodeHasSufficientPID     71m   kubelet, ubuntuserver20microk8s     Node ubuntuserver20microk8s status is now: NodeHasSufficientPID
  Warning  Rebooted                 71m   kubelet, ubuntuserver20microk8s     Node ubuntuserver20microk8s has been rebooted, boot id: 1726547b-ac97-42c0-adc4-40d31cc09f9b
  Normal   Starting                 26m   kube-proxy, ubuntuserver20microk8s  Starting kube-proxy.
  Normal   Starting                 26m   kubelet, ubuntuserver20microk8s     Starting kubelet.
  Warning  InvalidDiskCapacity      26m   kubelet, ubuntuserver20microk8s     invalid capacity 0 on image filesystem
  Normal   NodeHasSufficientMemory  26m   kubelet, ubuntuserver20microk8s     Node ubuntuserver20microk8s status is now: NodeHasSufficientMemory
  Normal   NodeHasNoDiskPressure    26m   kubelet, ubuntuserver20microk8s     Node ubuntuserver20microk8s status is now: NodeHasNoDiskPressure
  Normal   NodeHasSufficientPID     26m   kubelet, ubuntuserver20microk8s     Node ubuntuserver20microk8s status is now: NodeHasSufficientPID
  Normal   NodeAllocatableEnforced  26m   kubelet, ubuntuserver20microk8s     Updated Node Allocatable limit across pods
  Warning  Rebooted                 26m   kubelet, ubuntuserver20microk8s     Node ubuntuserver20microk8s has been rebooted, boot id: ea5f6c69-b1b9-4650-a819-a83e75ef71bc
```

`kubectl explain node`  
```
KIND:     Node
VERSION:  v1

DESCRIPTION:
     Node is a worker node in Kubernetes. Each node will have a unique
     identifier in the cache (i.e. in etcd).

FIELDS:
   apiVersion	<string>
     APIVersion defines the versioned schema of this representation of an
     object. Servers should convert recognized schemas to the latest internal
     value, and may reject unrecognized values. More info:
     https://git.k8s.io/community/contributors/devel/sig-architecture/api-conventions.md#resources

   kind	<string>
     Kind is a string value representing the REST resource this object
     represents. Servers may infer this from the endpoint the client submits
     requests to. Cannot be updated. In CamelCase. More info:
     https://git.k8s.io/community/contributors/devel/sig-architecture/api-conventions.md#types-kinds

   metadata	<Object>
     Standard object's metadata. More info:
     https://git.k8s.io/community/contributors/devel/sig-architecture/api-conventions.md#metadata

   spec	<Object>
     Spec defines the behavior of a node.
     https://git.k8s.io/community/contributors/devel/sig-architecture/api-conventions.md#spec-and-status

   status	<Object>
     Most recently observed status of the node. Populated by the system.
     Read-only. More info:
     https://git.k8s.io/community/contributors/devel/sig-architecture/api-conventions.md#spec-and-status
```

`kubectl get pods`  
No resources found in default namespace.

`kubectl get namespaces`  
```
NAME              STATUS   AGE
default           Active   166m
kube-node-lease   Active   166m
kube-public       Active   166m
kube-system       Active   166m
```

### Displaying pods

`kubectl get pods --all-namespaces`  
`kubectl get pods -A`  
`kubectl -n kube-public get configmaps`  

### Create Deployments

`kubectl create deployment pingpong --image=alpine`  
`kubectl delete deployment pingpong`  


`kubectl create deployment nginx --image=nginx`  


### Scaling


`kubectl scale deploy/nginx --replicas 3`  
```
kubectl get all
NAME                        READY   STATUS    RESTARTS   AGE
pod/nginx-f89759699-fl6pk   1/1     Running   0          18s
pod/nginx-f89759699-j6khh   1/1     Running   0          18s
pod/nginx-f89759699-p76w6   1/1     Running   0          55s

NAME                 TYPE        CLUSTER-IP     EXTERNAL-IP   PORT(S)   AGE
service/kubernetes   ClusterIP   10.152.183.1   <none>        443/TCP   3h16m

NAME                    READY   UP-TO-DATE   AVAILABLE   AGE
deployment.apps/nginx   3/3     3            3           55s

NAME                              DESIRED   CURRENT   READY   AGE
replicaset.apps/nginx-f89759699   3         3         3       55s
```

### Logs

`kubectl logs deployment/nginx`  
```
Found 3 pods, using pod/nginx-f89759699-p76w6
/docker-entrypoint.sh: /docker-entrypoint.d/ is not empty, will attempt to perform configuration
/docker-entrypoint.sh: Looking for shell scripts in /docker-entrypoint.d/
/docker-entrypoint.sh: Launching /docker-entrypoint.d/10-listen-on-ipv6-by-default.sh
10-listen-on-ipv6-by-default.sh: Getting the checksum of /etc/nginx/conf.d/default.conf
10-listen-on-ipv6-by-default.sh: Enabled listen on IPv6 in /etc/nginx/conf.d/default.conf
/docker-entrypoint.sh: Launching /docker-entrypoint.d/20-envsubst-on-templates.sh
/docker-entrypoint.sh: Configuration complete; ready for start up
```

`kubectl logs deployment/nginx --tail 10 --follow`  
```
Found 3 pods, using pod/nginx-f89759699-p76w6
/docker-entrypoint.sh: /docker-entrypoint.d/ is not empty, will attempt to perform configuration
/docker-entrypoint.sh: Looking for shell scripts in /docker-entrypoint.d/
/docker-entrypoint.sh: Launching /docker-entrypoint.d/10-listen-on-ipv6-by-default.sh
10-listen-on-ipv6-by-default.sh: Getting the checksum of /etc/nginx/conf.d/default.conf
10-listen-on-ipv6-by-default.sh: Enabled listen on IPv6 in /etc/nginx/conf.d/default.conf
/docker-entrypoint.sh: Launching /docker-entrypoint.d/20-envsubst-on-templates.sh
/docker-entrypoint.sh: Configuration complete; ready for start up
```

`watch kubectl get pods`
```

Every 2.0s: kubectl get pods

NAME                    READY   STATUS    RESTARTS   AGE
nginx-f89759699-2f67x   1/1     Running   0          3m13s
nginx-f89759699-fl6pk   1/1     Running   0          13m
nginx-f89759699-z5hzz   1/1     Running   0          4m43s
```

in new terminal delete one of the pods:
the new one started:
kubectl get pods
NAME                    READY   STATUS    RESTARTS   AGE
nginx-f89759699-fl6pk   1/1     Running   0          14m
nginx-f89759699-vmgtj   1/1     Running   0          18s
nginx-f89759699-z5hzz   1/1     Running   0          6m18s


### Cron Jobs

in vesion 17-
`kubectl run --schedule="*/3 * * * *" --restart=OnFailure --image=apache sleep 10`  
kubectl run --generator=cronjob/v1beta1 is DEPRECATED and will be removed in a future version. Use kubectl run --generator=run-pod/v1 or kubectl create instead.
cronjob.batch/sleep created


in vesion 18+
`kubectl create cronjob --schedule="*/3 * * * *" --restart=OnFailure --image=nginx longsleep`  
cronjob.batch/longsleep created



`kubectl get cronjobs`  
```
NAME        SCHEDULE      SUSPEND   ACTIVE   LAST SCHEDULE   AGE
longsleep   */3 * * * *   False     0        <none>          13s
sleep       */3 * * * *   False     0        <none>          61s
```
`kubectl get jobs`  
```
NAME                   COMPLETIONS   DURATION   AGE
longsleep-1594304100   0/1           3m         3m
longsleep-1594304280   0/1           9s         9s
sleep-1594304100       0/1           3m         3m
sleep-1594304280       0/1           9s         9s
```


in vresion 17 and less
`kubectl run pingpong --image=alpine ping 1.1.1.1`  
```
kubectl run --generator=deployment/apps.v1 is DEPRECATED and will be removed in a future version. Use kubectl run --generator=run-pod/v1 or kubectl create instead.
deployment.apps/pingpong created
```
`kubectl scale deploy/pingpong --replicas 3`  
```
kubectl get deployments
NAME       READY   UP-TO-DATE   AVAILABLE   AGE
pingpong   3/3     3            3           4m54s
```
```
kubectl logs -l run=pingpong --tail 1 -f
64 bytes from 1.1.1.1: seq=333 ttl=59 time=13.534 ms
64 bytes from 1.1.1.1: seq=300 ttl=59 time=11.888 ms
64 bytes from 1.1.1.1: seq=300 ttl=59 time=11.563 ms
64 bytes from 1.1.1.1: seq=301 ttl=59 time=11.304 ms
64 bytes from 1.1.1.1: seq=334 ttl=59 time=11.642 ms
```

`kubectl scale deploy/pingpong --replicas 8`  

```
kubectl logs -l run=pingpong --tail 1 -f
error: you are attempting to follow 8 log streams, but maximum allowed concurrency is 5, use --max-log-requests to increase the limit
```

### Stern
`sudo curl -L -o /usr/local/bin/stern https://github.com/wercker/stern/releases/download/1.11.0/stern_linux_amd64`  
`
`sudo chmod +x /usr/local/bin/stern`  
`stern pingpong`  
`stern --tail 1 --timestamps  pingpong`  


```
kubectl get nodes  
kubectl version  
kubectl describe node/tomasz-virtualbox  
kubectl get pod --namespace=kube-system  
kubectl create deployment ticktock --image=bretfisher/clock  
kubectl scale deployment/ticktock --replicas 3  
kubectl logs deploy/ticktock --tail 1  
```

### Expose
```
kubectl create deployment httpenv --image=bretfisher/httpenv  
kubectl scale deployment httpenv --replicas=10  
kubect get pods  
kubectl expose deployment httpenv --port 8888  
```
```
IP=$(kubectl get svc httpenv -o go-template --template '{{ .spec.clusterIP }}')
curl http://$IP:8888/
curl -s http://$IP:8888/ | jq .HOSTNAME
"httpenv-6bf64f7c4f-gwr8n"
```

### endpoints
```
kubectl describe service httpenv
kubectl get endpoints httpenv -o yaml
kubectl get pods -l app=httpenv -o wide

kubectl delete deployment/httpenv service/httpenv
```

### Networking


assigment:  
kubectl create deployment littletomcat --image=tomcat  
kubectl get pod -o wide  
or
kubectl get pods --selector=app=littletomcat -o wide
or
kubectl get pod/littletomcat-7c89b45579-8t7zk -o wide  
```
NAME                            READY   STATUS    RESTARTS   AGE    IP            NODE                     NOMINATED NODE   READINESS GATES
littletomcat-7c89b45579-8t7zk   1/1     Running   0          2m6s   10.1.50.179   ubuntuserver20microk8s   <none>           <none>
```
ping 10.1.50.179  
kubectl delete pod/littletomcat-7c89b45579-8t7zk  
or with:
kubectl delete pods --selector=app=littletomcat  
kubectl expose deployment littletomcat --port=8080  
or with:  
kubectl create service clusterip littletomcat --tcp 8080  
```
kubectl get services
NAME           TYPE        CLUSTER-IP       EXTERNAL-IP   PORT(S)    AGE
kubernetes     ClusterIP   10.152.183.1     <none>        443/TCP    21h
littletomcat   ClusterIP   10.152.183.122   <none>        8080/TCP   89s
```
curl http://10.152.183.122:8080   
clean up  
kubectl delete deployment.apps/littletomcat  
kubectl delete service/littletomcat  


### Sample Microservice: DockerCoins

kubectl create deployment redis --image=redis  
kubectl create deployment hasher --image=dockercoins/hasher:v0.1  
kubectl create deployment rng --image=dockercoins/rng:v0.1  
kubectl create deployment webui --image=dockercoins/webui:v0.1  
kubectl create deployment worker --image=dockercoins/worker:v0.1  


kubectl expose deployment redis --port 6379  
kubectl expose deployment rng --port 80   
kubectl expose deployment hasher --port 80  
kubectl logs deploy/worker --follow   

kubectl expose deploy/webui --type=NodePort --port=80  
```
kubectl get svc
NAME         TYPE        CLUSTER-IP       EXTERNAL-IP   PORT(S)        AGE
hasher       ClusterIP   10.152.183.239   <none>        80/TCP         2m33s
kubernetes   ClusterIP   10.152.183.1     <none>        443/TCP        22h
redis        ClusterIP   10.152.183.236   <none>        6379/TCP       3m23s
rng          ClusterIP   10.152.183.240   <none>        80/TCP         2m52s
webui        NodePort    10.152.183.110   <none>        80:31549/TCP   20s
```


in web browser type:   
localhost:31549  
or   
specify addres of the machine like:  
http://192.168.100.9:31549  

kubectl scale deploy/worker --replicas=2  
kubectl scale deploy/worker --replicas=3  
kubectl scale deploy/worker --replicas=10   
kubectl scale deploy/worker --replicas=3  
ok the performance dont go up anymore beyond 3 replicas  

HASHER=$(kubectl get svc hasher -o go-template={{.spec.clusterIP}})  
RNG=$(kubectl get svc rng -o go-template={{.spec.clusterIP}})  

httping -c 3 $HASHER
```
PING 10.152.183.239:80 (/):
connected to 10.152.183.239:80 (210 bytes), seq=0 time=  1.71 ms 
connected to 10.152.183.239:80 (210 bytes), seq=1 time=  2.17 ms 
connected to 10.152.183.239:80 (210 bytes), seq=2 time=  1.07 ms 
--- http://10.152.183.239/ ping statistics ---
3 connects, 3 ok, 0.00% failed, time 3007ms
round-trip min/avg/max = 1.1/1.6/2.2 ms
```
```
httping -c 3 $RNG
PING 10.152.183.240:80 (/):
connected to 10.152.183.240:80 (158 bytes), seq=0 time= 52.53 ms 
connected to 10.152.183.240:80 (158 bytes), seq=1 time=135.04 ms 
connected to 10.152.183.240:80 (158 bytes), seq=2 time= 71.20 ms 
--- http://10.152.183.240/ ping statistics ---
3 connects, 3 ok, 0.00% failed, time 3265ms
round-trip min/avg/max = 52.5/86.3/135.0 ms
```

so we need to scal up the rng deployment  
kubectl scale deploy/rng --replicas=3

break-time-to-do-assigment
wordsmith app

kubectl create deployment wordsmith-web --image=bretfisher/wordsmith-web
kubectl create deployment wordsmith-words --image=bretfisher/wordsmith-words
kubectl create deployment wordsmith-db --image=bretfisher/wordsmith-db

kubectl expose deploy/wordsmith-db --port 5432
kubectl expose deploy/wordsmith-words --port 8080
kubectl expose deploy/wordsmith-web --type=NodePort --port=80

kubectl get svc

kubectl scale deploy/wordsmith-words --replicas=5


### YAML

kubectl apply -f https://k8smastery.com/dockercoins.yaml


### Kubernetes Dashboard

kubectl apply -f https://k8smastery.com/insecure-dashboard.yaml
```
kubectl get service dashboard
NAME        TYPE       CLUSTER-IP      EXTERNAL-IP   PORT(S)        AGE
dashboard   NodePort   10.152.183.89   <none>        80:30818/TCP   27s
```


### DaemonSets
kubectl get deploy/rng -o yaml > rng.yml  
change the kind from Deployment do DaemonSet save file  
kubectl apply -f rng.yml  
```
error: error validating "rng.yml": error validating data: [ValidationError(DaemonSet.spec): unknown field "progressDeadlineSeconds" in io.k8s.api.apps.v1.DaemonSetSpec, ValidationError(DaemonSet.spec): unknown field "replicas" in io.k8s.api.apps.v1.DaemonSetSpec, ValidationError(DaemonSet.spec): unknown field "strategy" in io.k8s.api.apps.v1.DaemonSetSpec, ValidationError(DaemonSet.status): unknown field "availableReplicas" in io.k8s.api.apps.v1.DaemonSetStatus, ValidationError(DaemonSet.status.conditions[0]): unknown field "lastUpdateTime" in io.k8s.api.apps.v1.DaemonSetCondition, ValidationError(DaemonSet.status.conditions[1]): unknown field "lastUpdateTime" in io.k8s.api.apps.v1.DaemonSetCondition, ValidationError(DaemonSet.status): unknown field "readyReplicas" in io.k8s.api.apps.v1.DaemonSetStatus, ValidationError(DaemonSet.status): unknown field "replicas" in io.k8s.api.apps.v1.DaemonSetStatus, ValidationError(DaemonSet.status): unknown field "updatedReplicas" in io.k8s.api.apps.v1.DaemonSetStatus, ValidationError(DaemonSet.status): missing required field "currentNumberScheduled" in io.k8s.api.apps.v1.DaemonSetStatus, ValidationError(DaemonSet.status): missing required field "numberMisscheduled" in io.k8s.api.apps.v1.DaemonSetStatus, ValidationError(DaemonSet.status): missing required field "desiredNumberScheduled" in io.k8s.api.apps.v1.DaemonSetStatus, ValidationError(DaemonSet.status): missing required field "numberReady" in io.k8s.api.apps.v1.DaemonSetStatus]; if you choose to ignore these errors, turn validation off with --validate=false
```
to ignore these error you can use option:
--validate=false
```
kubectl apply -f rng.yml --validate=false
daemonset.apps/rng created
```

```
kubectl get daemonset
NAME   DESIRED   CURRENT   READY   UP-TO-DATE   AVAILABLE   NODE SELECTOR   AGE
rng    1         1         1       1            1           <none>          2m55s
```

### Labels and selectors
```
kubectl get pods -l app=rng
NAME                   READY   STATUS    RESTARTS   AGE
rng-6979b4858b-qwr7s   1/1     Running   3          2d8h
rng-ljgx5              1/1     Running   0          29m
```
```
kubectl label pods -l app=rng enabled=yes
pod/rng-6979b4858b-qwr7s labeled
pod/rng-ljgx5 labeled
```
kubectl edit service rng

go under spec  
selector  
app  
add:  
`enabled: "yes"`  
save: wq :)  

service/rng edited  


POD=$(kubectl get pod -l app=rng,pod-template-hash -o name)

kubectl logs --tail 1 --follow $POD

kubectl label pod -l app=rng,pod-template-hash enabled-
pod/rng-6979b4858b-qwr7s labeled

cleanup the mess ;)  
kubectl delete -f https://k8smastery.com/dockercoins.yaml  

kubectl delete daemonset/rng  


#### assigment labels and selectors

kubectl create deployment v1-nginx --image=nginx  
kubectl expose deployment v1-nginx --type=NodePort --port=80   

```
kubectl get svc v1-nginx
NAME       TYPE       CLUSTER-IP       EXTERNAL-IP   PORT(S)        AGE
v1-nginx   NodePort   10.152.183.108   <none>        80:32297/TCP   61s
```
curl 10.152.183.135  

okey edit nginx service 
kubectl edit service v1-nginx  

Look for the `selector:` section, and change `app: v1-nginx` to `myapp: web`. Make sure to change the selector:

edite the deployment of v1-nginx
kubectl edit deployment v1-nginx

look for `labels:` selecetor within the `template:` section
add `myapp: web` bellow `app: v1-nginx`    
save  

add next deployment
kubectl create deployment v2-apache --image=httpd  
edit like before   
kubect edit deployment v2-apache  
`template:` -> `labels:`
add  
`myapp: web` bellow `app: v2-apache`

check the: http://localhost:32297  
okey clean up.