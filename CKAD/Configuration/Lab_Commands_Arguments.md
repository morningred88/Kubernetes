# Lab - Commands and Arguments

## How many PODs exist on the system?

in the current(default) namespace

```
➜  kubectl get pods
NAME             READY   STATUS    RESTARTS   AGE
ubuntu-sleeper   1/1     Running   0          79s
```

Answer: 1

## What is the command used to run the pod `ubuntu-sleeper`?

```
➜  kubectl describe pod ubuntu-sleeper
Name:         ubuntu-sleeper
Namespace:    default
Priority:     0
Node:         controlplane/172.25.0.32
Start Time:   Tue, 03 May 2022 01:48:31 +0000
Labels:       <none>
Annotations:  <none>
Status:       Running
IP:           10.42.0.9
IPs:
  IP:  10.42.0.9
Containers:
  ubuntu:
    Container ID:  containerd://b045222cf1d62ca597ec13343e09e5f2bbf2653d68e0331e6046840b6dd6354b
    Image:         ubuntu
    Image ID:      docker.io/library/ubuntu@sha256:26c68657ccce2cb0a31b330cb0be2b5e108d467f641c62e13ab40cbec258c68d
    Port:          <none>
    Host Port:     <none>
    Command:
      sleep
      4800
    State:          Running
      Started:      Tue, 03 May 2022 01:48:38 +0000
    Ready:          True
    Restart Count:  0
    Environment:    <none>
    Mounts:
      /var/run/secrets/kubernetes.io/serviceaccount from kube-api-access-rgmpx (ro)
Conditions:
  Type              Status
  Initialized       True 
  Ready             True 
  ContainersReady   True 
  PodScheduled      True 
Volumes:
  kube-api-access-rgmpx:
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
  Normal  Scheduled  2m49s  default-scheduler  Successfully assigned default/ubuntu-sleeper to controlplane
  Normal  Pulling    2m48s  kubelet            Pulling image "ubuntu"
  Normal  Pulled     2m43s  kubelet            Successfully pulled image "ubuntu" in 4.835728708s
  Normal  Created    2m43s  kubelet            Created container ubuntu
  Normal  Started    2m42s  kubelet            Started container ubuntu
```

Answer: sleep 4800

## Create a pod with the ubuntu image to run a container to sleep for 5000 seconds. Modify the file `ubuntu-sleeper-2.yaml`.

Note: Only make the necessary changes. Do not modify the name.

Correct `ubuntu-sleeper-2.yaml` file: 

```
➜  vi ubuntu-sleeper-2.yaml 
apiVersion: v1
kind: Pod 
metadata:
  name: ubuntu-sleeper-2
spec:
  containers:
  - name: ubuntu
    image: ubuntu
```

Add command to the file

```
apiVersion: v1
kind: Pod 
metadata:
  name: ubuntu-sleeper-2
spec:
  containers:
  - name: ubuntu
    image: ubuntu
    command: ["sleep","5000"]
```

Create pod using `ubuntu-sleeper-2.yaml`:

```
➜  kubectl apply -f ubuntu-sleeper-2.yaml 
pod/ubuntu-sleeper-2 created
```

Verify the pod `ubuntu-sleeper-2`

```
➜  kubectl describe pod  ubuntu-sleeper-2
Name:         ubuntu-sleeper-2
Namespace:    default
Priority:     0
Node:         controlplane/172.25.0.32
Start Time:   Tue, 03 May 2022 02:07:52 +0000
Labels:       <none>
Annotations:  <none>
Status:       Running
IP:           10.42.0.10
IPs:
  IP:  10.42.0.10
Containers:
  ubuntu:
    Container ID:  containerd://793bbd3130fb737558f7a7d02651bd7bf02148d304505b52737f523ac90288f2
    Image:         ubuntu
    Image ID:      docker.io/library/ubuntu@sha256:26c68657ccce2cb0a31b330cb0be2b5e108d467f641c62e13ab40cbec258c68d
    Port:          <none>
    Host Port:     <none>
    Command:
      sleep
      5000
    State:          Running
      Started:      Tue, 03 May 2022 02:07:53 +0000
    Ready:          True
    Restart Count:  0
    Environment:    <none>
    Mounts:
      /var/run/secrets/kubernetes.io/serviceaccount from kube-api-access-x2kh4 (ro)
Conditions:
  Type              Status
  Initialized       True 
  Ready             True 
  ContainersReady   True 
  PodScheduled      True 
Volumes:
  kube-api-access-x2kh4:
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
  Normal  Scheduled  15m   default-scheduler  Successfully assigned default/ubuntu-sleeper-2 to controlplane
  Normal  Pulling    15m   kubelet            Pulling image "ubuntu"
  Normal  Pulled     15m   kubelet            Successfully pulled image "ubuntu" in 537.527837ms
  Normal  Created    15m   kubelet            Created container ubuntu
  Normal  Started    15m   kubelet            Started container ubuntu
```



## Create a pod using the file named `ubuntu-sleeper-3.yaml`. There is something wrong with it. Try to fix it!

Note: Only make the necessary changes. Do not modify the name.

Using vim to open the yaml file: 

```
➜  vi ubuntu-sleeper-3.yaml
apiVersion: v1
kind: Pod 
metadata:
  name: ubuntu-sleeper-3
spec:
  containers:
  - name: ubuntu
    image: ubuntu
    command: 
      - "sleep"
      - 1200
```

Correct commands - add double quote for 1200:

```
➜  vi ubuntu-sleeper-3.yaml "
```

Create pod using `ubuntu-sleeper-3.yaml`:

```
➜  kubectl apply -f ubuntu-sleeper-3.yaml 
pod/ubuntu-sleeper-3 created
```

Verify the pod `ubuntu-sleeper-3`

```
➜  kubectl describe pod ubuntu-sleeper-3
Name:         ubuntu-sleeper-3
Namespace:    default
Priority:     0
Node:         controlplane/172.25.0.32
Start Time:   Tue, 03 May 2022 02:19:23 +0000
Labels:       <none>
Annotations:  <none>
Status:       Running
IP:           10.42.0.11
IPs:
  IP:  10.42.0.11
Containers:
  ubuntu:
    Container ID:  containerd://004cb1670b8005e67af8ec69c27c975e20364d18a77e6799a1c4f9b7a5f952f4
    Image:         ubuntu
    Image ID:      docker.io/library/ubuntu@sha256:26c68657ccce2cb0a31b330cb0be2b5e108d467f641c62e13ab40cbec258c68d
    Port:          <none>
    Host Port:     <none>
    Command:
      sleep
      1200
    State:          Running
      Started:      Tue, 03 May 2022 02:19:25 +0000
    Ready:          True
    Restart Count:  0
    Environment:    <none>
    Mounts:
      /var/run/secrets/kubernetes.io/serviceaccount from kube-api-access-tlhwb (ro)
Conditions:
  Type              Status
  Initialized       True 
  Ready             True 
  ContainersReady   True 
  PodScheduled      True 
Volumes:
  kube-api-access-tlhwb:
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
  Normal  Scheduled  36s   default-scheduler  Successfully assigned default/ubuntu-sleeper-3 to controlplane
  Normal  Pulling    35s   kubelet            Pulling image "ubuntu"
  Normal  Pulled     35s   kubelet            Successfully pulled image "ubuntu" in 541.718201ms
  Normal  Created    35s   kubelet            Created container ubuntu
  Normal  Started    34s   kubelet            Started container ubuntu
```

## Update pod `ubuntu-sleeper-3` to sleep for 2000 seconds.

Note: Only make the necessary changes. Do not modify the name of the pod. Delete and recreate the pod if necessary.

First, delete pod ubuntu-sleeper-3

```
 ➜  kubectl delete pod ubuntu-sleeper-3
pod "ubuntu-sleeper-3" deleted

```

Then, edit the yaml file

```
➜  vi ubuntu-sleeper-3.yaml 
apiVersion: v1
kind: Pod 
metadata:
  name: ubuntu-sleeper-3
spec:
  containers:
  - name: ubuntu
    image: ubuntu
    command: 
      - "sleep"
      - "2000"
```

Create the pod

```
➜  kubectl apply -f ubuntu-sleeper-3.yaml 
pod/ubuntu-sleeper-3 created
```

## Inspect the file `Dockerfile` given at `/root/webapp-color` directory. What command is run at container startup?

```
➜  cat webapp-color/Dockerfile
FROM python:3.6-alpine

RUN pip install flask

COPY . /opt/

EXPOSE 8080

WORKDIR /opt

ENTRYPOINT ["python", "app.py"]
```

Answer: python app.py

## Inspect the file `Dockerfile2` given at `/root/webapp-color` directory. What command is run at container startup?

```
➜   cat webapp-color/Dockerfile2
FROM python:3.6-alpine

RUN pip install flask

COPY . /opt/

EXPOSE 8080

WORKDIR /opt

ENTRYPOINT ["python", "app.py"]

CMD ["--color", "red"]
```

Answer: python app.py --color red

## Inspect the two files under directory `webapp-color-2`. What command is run at container startup?

Assume the image was created from the Dockerfile in this folder.

Open folder webapp-color-2

```
➜  cd webapp-color-2
~/webapp-color-2 ➜  ls
Dockerfile2            webapp-color-pod.yaml
```

Read `Dockerfile2` file:

```
~/webapp-color-2 ➜  cat Dockerfile2 
FROM python:3.6-alpine

RUN pip install flask

COPY . /opt/

EXPOSE 8080

WORKDIR /opt

ENTRYPOINT ["python", "app.py"]

CMD ["--color", "red"]
```

Container start up command = ENTRYPOINT + CMD

Read `webapp-color-pod.yaml` file

```
~/webapp-color-2 ➜  cat webapp-color-pod.yaml 
apiVersion: v1 
kind: Pod 
metadata:
  name: webapp-green
  labels:
      name: webapp-green 
spec:
  containers:
  - name: simple-webapp
    image: kodekloud/webapp-color
    command: ["--color","green"]
```

command in pod definition file will overwrite ENTRYPOINT in container file. 

Args in pod definition file  will overwrite CMD in container file. 

So the answer is: --color green

## Inspect the two files under directory `webapp-color-3`. What command is run at container startup?

Assume the image was created from the Dockerfile in this folder.

Get the files in the folder 

```
➜  ls webapp-color-3
Dockerfile2              webapp-color-pod-2.yaml
```

Open docker file

```
➜  cat webapp-color-3/Dockerfile2 
FROM python:3.6-alpine

RUN pip install flask

COPY . /opt/

EXPOSE 8080

WORKDIR /opt

ENTRYPOINT ["python", "app.py"]

CMD ["--color", "red"]
```

Open pod definition file

```
➜  cat webapp-color-3/webapp-color-pod-2.yaml 
apiVersion: v1 
kind: Pod 
metadata:
  name: webapp-green
  labels:
      name: webapp-green 
spec:
  containers:
  - name: simple-webapp
    image: kodekloud/webapp-color
    command: ["python", "app.py"]
    args: ["--color", "pink"]
```

The answer is: python app.py --color pink

## Create a pod with the given specifications. By default it displays a `blue` background. Set the given command line arguments to change it to `green`

- Pod Name: webapp-green
- Image: kodekloud/webapp-color
- Command line arguments: --color=green

Create a pod.yaml file

```
➜  kubectl run webapp-green --image=kodekloud/webapp-color --dry-run=client -o yaml > pod.yaml
```

Update pod.yaml file - add args

```
sf
apiVersion: v1
kind: Pod
metadata:
  creationTimestamp: null
  labels:
    run: webapp-green
  name: webapp-green
spec:
  containers:
  - image: kodekloud/webapp-color
    name: webapp-green
    args: ["--color", "green"]
    resources: {}
  dnsPolicy: ClusterFirst
  restartPolicy: Always
status: {}
```

Create the pod:

```
➜  kubectl apply -f pod.yaml
pod/webapp-green created
```

