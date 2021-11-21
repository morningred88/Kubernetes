# Lab-Pod

## How many `pods` exist on the system?

```
kubectl get pods
```

Result:

```
No resources found in default namespace.
```

So there is no pods available. 

## Create a new pod with the `nginx` image.

```
kubectl run nginx --image=nginx
```

Result:

```
pod/nginx created
```

## How many pods are created now?

Note: We have created a few more pods. So please check again.

```
kubectl get pods
```

Result:

```
NAME            READY   STATUS    RESTARTS   AGE
nginx           1/1     Running   0          68s
newpods-w785g   1/1     Running   0          54s
newpods-gf5m9   1/1     Running   0          54s
newpods-8866j   1/1     Running   0          54s
```

So answer is 4

## What is the image used to create the new pods?

You must look at one of the new pods in detail to figure this out.

```
kubectl describe pod newpods-w785g |grep -i image
```

Result:

```
   Image:         busybox
    Image ID:      docker.io/library/busybox@sha256:d2b53584f580310186df7a2055ce3ff83cc0df6caacf1e3489bff8cf5d0af5d8
  Normal  Pulling    6m41s  kubelet            Pulling image "busybox"
  Normal  Pulled     6m36s  kubelet            Successfully pulled image "busybox" in 5.243541985s
```

Answer: busybox

## Which nodes are these pods placed on?

You must look at all the pods in detail to figure this out.

Best solution:

```
kubectl get pods -o wide
```

Result: 

```
NAME            READY   STATUS             RESTARTS       AGE     IP           NODE           NOMINATED NODE   READINESS GATES
nginx           1/1     Running            0              27m     10.42.0.9    controlplane   <none>           <none>
newpods-9zwp2   1/1     Running            1 (101s ago)   18m     10.42.0.11   controlplane   <none>           <none>
newpods-64k2h   1/1     Running            1 (101s ago)   18m     10.42.0.10   controlplane   <none>           <none>
newpods-9vs5c   1/1     Running            1 (101s ago)   18m     10.42.0.12   controlplane   <none>           <none>
```

Answer: controlplane

You can also use `describe pod` to get the answer, but you need to run `describe pod` command for each pods.

```
kubectl describe pod pod-name |grep -i node
```

## How many container of the new new `webapp` pod?

Note: We just created a new POD. Ignore the state of the POD for now.

```
kubectl get pods webapp
```

Result

```
NAME     READY   STATUS             RESTARTS   AGE
webapp   1/2     ImagePullBackOff   0          16m
```

You can see from Ready 1 of 2 apps are ready. So total 2 containers

## What images are used in the new `webapp` pod?

**You can also use `describe pod` to get the answer**

```
kubectl describe pod webapp
```

Result:

```
Name:         webapp
Namespace:    default
Priority:     0
Node:         controlplane/172.25.0.41
Start Time:   Sat, 30 Apr 2022 21:18:57 +0000
Labels:       <none>
Annotations:  <none>
Status:       Pending
IP:           10.42.0.13
IPs:
  IP:  10.42.0.13
Containers:
  nginx:
    Container ID:   containerd://dc2cbbf627431da73a0de7ea5222cb49be5b693be26dcd91c717aaa626d259a9
    Image:          nginx
    Image ID:       docker.io/library/nginx@sha256:859ab6768a6f26a79bc42b231664111317d095a4f04e4b6fe79ce37b3d199097
    Port:           <none>
    Host Port:      <none>
    State:          Running
      Started:      Sat, 30 Apr 2022 21:18:59 +0000
    Ready:          True
    Restart Count:  0
    Environment:    <none>
    Mounts:
      /var/run/secrets/kubernetes.io/serviceaccount from kube-api-access-5nf9t (ro)
  agentx:
    Container ID:   
    Image:          agentx
    Image ID:       
    Port:           <none>
    Host Port:      <none>
    State:          Waiting
      Reason:       ImagePullBackOff
    Ready:          False
    Restart Count:  0
    Environment:    <none>
    Mounts:
      /var/run/secrets/kubernetes.io/serviceaccount from kube-api-access-5nf9t (ro)
Conditions:
  Type              Status
  Initialized       True 
  Ready             False 
  ContainersReady   False 
  PodScheduled      True 
Volumes:
  kube-api-access-5nf9t:
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
  Type     Reason     Age                From               Message
  ----     ------     ----               ----               -------
  Normal   Scheduled  52s                default-scheduler  Successfully assigned default/webapp to controlplane
  Normal   Pulling    51s                kubelet            Pulling image "nginx"
  Normal   Pulled     51s                kubelet            Successfully pulled image "nginx" in 517.441871ms
  Normal   Created    51s                kubelet            Created container nginx
  Normal   Started    50s                kubelet            Started container nginx
  Normal   BackOff    24s (x3 over 50s)  kubelet            Back-off pulling image "agentx"
  Warning  Failed     24s (x3 over 50s)  kubelet            Error: ImagePullBackOff
  Normal   Pulling    9s (x3 over 50s)   kubelet            Pulling image "agentx"
  Warning  Failed     9s (x3 over 50s)   kubelet            Failed to pull image "agentx": rpc error: code = Unknown desc = failed to pull and unpack image "docker.io/library/agentx:latest": failed to resolve reference "docker.io/library/agentx:latest": pull access denied, repository does not exist or may require authorization: server message: insufficient_scope: authorization failed
  Warning  Failed     9s (x3 over 50s)   kubelet            Error: ErrImagePull
```

Answer: nginx and agentx 

## What is the state of the container `agentx` in the pod `webapp`?

Wait for it to finish the `ContainerCreating` state

From the result above you can see the `state` is `Waiting`.

## Why do you think the container `agentx` in pod `webapp` is in error?

Try to figure it out from the events section of the pod.

Answer: 

From the result above, docker image does not exist. 

## What does the READY column in the output of the `kubectl get pods` command indicate?

Running containers in a pod/total containers in a pod

Delete the `webapp` Pod.

Once deleted, wait for the pod to fully terminate.

```
kubectl delete pod webapp
pod "webapp" deleted
```

## Create a new pod with the name `redis` and with the image `redis123`.

Use a pod-definition YAML file. And yes the image name is wrong!

Create pod.yaml file without creating a pod

```
kubectl run redis --image=redis123 --dry-run=client -o yaml > pod.yaml
```

Open the file with vi editor to check the pod.yaml file

```
vi pod.yaml
```

Check the file, save and exit

```
:wq
```

Then create a pod with the pod.yaml file:

```
kubectl apply -f pod.yaml
```

Result

```
pod/redis created
```



## Now change the image on this pod to `redis`.

Once done, the pod should be in a `running` state.

```
kubectl edit pot redis
```

It will open the running configuration in  vi editor

Change the image in spec part from redis123 to redis

verify 

```
kubectl get pods
```

Result: 

```
NAME            READY   STATUS    RESTARTS      AGE
nginx           1/1     Running   0             47m
newpods-w785g   1/1     Running   2 (13m ago)   47m
newpods-gf5m9   1/1     Running   2 (13m ago)   47m
newpods-8866j   1/1     Running   2 (13m ago)   47m
redis           1/1     Running   0             8m57s
```

