# Lab - Imperative commands

Imperative: create a pod, deployment or service without a definition file.

Declarative: create a pod, deployment or service with a definition file.

## In this lab, you will get hands-on practice with creating Kubernetes objects imperatively.

All the questions in this lab can be done imperatively. However, for some questions, you may need to first create the YAML file using imperative methods. You can then modify the YAML according to the need and create the object using `kubectl apply -f` command.

## Deploy a pod named `nginx-pod` using the `nginx:alpine` image.

Use imperative commands only.

- Name: nginx-pod
- Image: nginx:alpine

**Solution 1: without creating yaml file**

```
➜  kubectl run nginx-pod --image=nginx:alpine
pod/nginx-pod created
```

**Solution 2: with creating yaml file**

```
➜  kubectl run nginx-pod --image=nginx:alpine --dry-run -o yaml > nginx-pod.yaml
W0502 13:51:08.725000    5772 helpers.go:598] --dry-run is deprecated and can be replaced with --dry-run=client.
```

nginx-pod.yaml has been created even the error message:

```
 ➜  ls
nginx-pod.yaml  sample.yaml
```

Check if `nginx-pod` has been created

```
➜  kubectl get pods
No resources found in default namespace.
```

Create `nginx-pod`

```
 ➜  kubectl apply -f nginx-pod.yaml 
pod/nginx-pod created
```

Verify the pod:

```
➜  kubectl get pods
NAME        READY   STATUS              RESTARTS   AGE
nginx-pod   0/1     ContainerCreating   0          5s
```

## Deploy a `redis` pod using the `redis:alpine` image with the labels set to `tier=db`.

Either use imperative commands to create the pod with the labels. Or else use imperative commands to generate the pod definition file, then add the labels before creating the pod using the file.

**Solution 1: without creating yaml file**

```
➜  kubectl run redis --image=redis:alpine --labels=tier=db
pod/redis created
```

**Solution 2: with creating yaml file**

```
kubectl run redis --image=redis:alpine --dry-run=client -o yaml > redis.yaml
```

Use vim edit `redis.yaml` file

```
➜  vi redis.yaml 
```

Add `tire=db` to `label` section:

```
apiVersion: v1
kind: Pod
metadata:
  creationTimestamp: null
  labels:
    run: redis
    tier: db
  name: redis
spec:
  containers:
  - image: redis:alpine
    name: redis
    resources: {}
  dnsPolicy: ClusterFirst
  restartPolicy: Always
status: {}
```

**Create pod**

```
➜  kubectl apply -f redis.yaml 
pod/redis created
```

## Create a service `redis-service` to expose the `redis` application within the cluster on port `6379`.

Use imperative commands.

- Service: redis-service
- Port: 6379
- Type: ClusterIP

**Solution 1: without creating yaml file**

```
➜  kubectl expose pod redis --port=6379 --name=redis-service
service/redis-service exposed
```

Verify the created `redis-service`

```
 ➜  kubectl describe svc redis-service
Name:              redis-service
Namespace:         default
Labels:            tier=db
Annotations:       <none>
Selector:          tier=db
Type:              ClusterIP
IP Family Policy:  SingleStack
IP Families:       IPv4
IP:                10.43.123.1
IPs:               10.43.123.1
Port:              <unset>  6379/TCP
TargetPort:        6379/TCP
Endpoints:         10.42.0.11:6379
Session Affinity:  None
Events:            <none>
```

You can see TargetPort is  6379, and has an endpoint 10.42.0.11:6379. 

Delete `redis-service`  and be ready for solution 2

```
➜  kubectl delete svc redis-service
service "redis-service" deleted
```

**Solution 2: with creating yaml file**

This following command's output is yaml file. But the yaml files does not created.

```
➜  kubectl expose pod redis --port=6379 --name=redis-service --dry-run=client -o yaml
apiVersion: v1
kind: Service
metadata:
  creationTimestamp: null
  labels:
    run: redis
    tier: db
  name: redis-service
spec:
  ports:
  - port: 6379
    protocol: TCP
    targetPort: 6379
  selector:
    run: redis
    tier: db
status:
  loadBalancer: {}
```

I checked, service definition yaml file does not created

Add  `redis-service.yaml` to the command. This time you cannot see the yaml output

```
➜  kubectl expose pod redis --port=6379 --name=redis-service --dry-run=client -o yaml >  redis-service.yaml
```

Verify if `redis-service.yaml` created

```
➜  ls
nginx-pod.yaml      redis-service.yaml  redis.yaml          sample.yaml
```

Create `redis-service`

```
➜  kubectl apply -f redis-service.yaml 
service/redis-service created
```

## Create a deployment named `webapp` using the image `kodekloud/webapp-color` with `3` replicas.

Try to use imperative commands only. Do not create definition files.

```
 ➜  kubectl create deployment webapp --image=kodekloud/webapp-color --replicas=3
deployment.apps/webapp created
```

Verify the deployment:

```
kubectl describe deployment webapp
```



## Create a new pod called `custom-nginx` using the `nginx` image and expose it on `container port 8080`.

```
➜  kubectl run custom-nginx --image=nginx --port=8080
pod/custom--nginx created
```

Verify the pod:

```
➜  kubectl describe pod custom-nginx

Name:         custom--nginx
Namespace:    default
Priority:     0
Node:         controlplane/172.25.0.49
Start Time:   Mon, 02 May 2022 17:00:36 +0000
Labels:       run=custom--nginx
Annotations:  <none>
Status:       Running
IP:           10.42.0.15
IPs:
  IP:  10.42.0.15
Containers:
  custom--nginx:
    Container ID:   containerd://71c00731e960f2f7c924f5751eecc85d59d4765ddc8b226a07a9582d79409935
    Image:          nginx
    Image ID:       docker.io/library/nginx@sha256:859ab6768a6f26a79bc42b231664111317d095a4f04e4b6fe79ce37b3d199097
    Port:           8080/TCP
    Host Port:      0/TCP
    State:          Running
      Started:      Mon, 02 May 2022 17:00:57 +0000
    Ready:          True
    Restart Count:  0
    Environment:    <none>
    Mounts:
      /var/run/secrets/kubernetes.io/serviceaccount from kube-api-access-sf2mw (ro)
Conditions:
  Type              Status
  Initialized       True 
  Ready             True 
  ContainersReady   True 
  PodScheduled      True 
Volumes:
  kube-api-access-sf2mw:
    Type:                    Projected (a volume that contains injected data from multiple sources)
    TokenExpirationSeconds:  3607
    ConfigMapName:           kube-root-ca.crt
    ConfigMapOptional:       <nil>
    DownwardAPI:             true
QoS Class:                   BestEffort
Node-Selectors:              <none>
Tolerations:                 node.kubernetes.io/not-ready:NoExecute op=Exists for 300s
                             node.kubernetes.io/unreachable:NoExecute op=Exists for 300s
Events:
  Type    Reason     Age    From               Message
  ----    ------     ----   ----               -------
  Normal  Scheduled  3m28s  default-scheduler  Successfully assigned default/custom--nginx to controlplane
  Normal  Pulling    3m18s  kubelet            Pulling image "nginx"
  Normal  Pulled     3m9s   kubelet            Successfully pulled image "nginx" in 9.824545814s
  Normal  Created    3m9s   kubelet            Created container custom--nginx
  Normal  Started    3m8s   kubelet            Started container custom--nginx
```

## Create a new namespace called `dev-ns`.

Use imperative commands.

```
➜  kubectl create ns dev-ns
namespace/dev-ns created
```

## Create a new deployment called `redis-deploy` in the `dev-ns` namespace with the `redis` image. It should have `2` replicas.

Use imperative commands.

```
➜  kubectl create deployment redis-deploy --image=redis --replicas=2 -n dev-ns
deployment.apps/redis-deploy created
```

## Create a pod called `httpd` using the image `httpd:alpine` in the default namespace. Next, create a service of type `ClusterIP` by the same name `(httpd)`. The target port for the service should be `80`.

Try to do this with as few steps as possible.

**Solution1: 1 step**

```
➜  kubectl run httpd --image=httpd:alpine --expose --port=80
service/httpd created
pod/httpd created
```



**Solution 2: 2 steps**

Create pod `httpd`

```
➜  kubectl run httpd --image=httpd:alpine
pod/httpd created
```

Check the pod 

```
➜  kubectl describe pod httpd
Name:         httpd
Namespace:    default
Priority:     0
Node:         controlplane/172.25.0.66
Start Time:   Mon, 02 May 2022 18:18:09 +0000
Labels:       run=httpd
Annotations:  <none>
Status:       Running
IP:           10.42.0.18
IPs:
  IP:  10.42.0.18
Containers:
  httpd:
    Container ID:   containerd://a43a209fb7dafe820e7ef1dcbe6063e6eaf02f295dee070eb8b8c511328e6e08
    Image:          httpd:alpine
    Image ID:       docker.io/library/httpd@sha256:4eb4177b9245c686696dd8120c79cd64b7632b27d890db4cad3b0e844ed737af
    Port:           <none>
    Host Port:      <none>
    State:          Running
      Started:      Mon, 02 May 2022 18:18:17 +0000
    Ready:          True
    Restart Count:  0
    Environment:    <none>
    Mounts:
      /var/run/secrets/kubernetes.io/serviceaccount from kube-api-access-fv5k9 (ro)
Conditions:
  Type              Status
  Initialized       True 
  Ready             True 
  ContainersReady   True 
  PodScheduled      True 
Volumes:
  kube-api-access-fv5k9:
    Type:                    Projected (a volume that contains injected data from multiple sources)
    TokenExpirationSeconds:  3607
    ConfigMapName:           kube-root-ca.crt
    ConfigMapOptional:       <nil>
    DownwardAPI:             true
QoS Class:                   BestEffort
Node-Selectors:              <none>
Tolerations:                 node.kubernetes.io/not-ready:NoExecute op=Exists for 300s
                             node.kubernetes.io/unreachable:NoExecute op=Exists for 300s
Events:
  Type    Reason     Age   From               Message
  ----    ------     ----  ----               -------
  Normal  Scheduled  84s   default-scheduler  Successfully assigned default/httpd to controlplane
  Normal  Pulling    83s   kubelet            Pulling image "httpd:alpine"
  Normal  Pulled     77s   kubelet            Successfully pulled image "httpd:alpine" in 6.405956479s
  Normal  Created    77s   kubelet            Created container httpd
  Normal  Started    76s   kubelet            Started container httpd
```

Create `httpd` serive

```
➜   kubectl expose pod httpd --port=80 
service/httpd exposed
```

