# Lab-replicasets

Frequent used commands for replicasets

```
kubectl create -f replicaset-definition.yml
kubectl get replicasets
kubectl describe replicaset myapp-replicaset
kubectl delete replicaset myapp-replicaset
kubectl replace replicaset-definition.yml
kubectl scale --replicas=6 -f replicaset-definition.yml
kubectl scale replicaset --replicas=6 myapp-replicaset
```

## How many PODs exist on the system?

In the current(default) namespace.

```
➜ kubectl get pods
No resources found in default namespace.
```

Answer: 0

## How many replicasets exist on the system?

```
➜ kubectl get replicasets
No resources found in default namespace.
```



## How about now? How many ReplicaSets do you see?

We just made a few changes!

```
➜  kubectl get replicaset
NAME              DESIRED   CURRENT   READY   AGE
new-replica-set   4         4         0       36s
```

Answer: 1

## How many PODs are DESIRED in the `new-replica-set`?

We can get the answer from command above. 

Answer: 4

## What is the image used to create the pods in the `new-replica-set`?

```
➜  kubectl describe replicaset new-replica-set
Name:         new-replica-set
Namespace:    default
Selector:     name=busybox-pod
Labels:       <none>
Annotations:  <none>
Replicas:     4 current / 4 desired
Pods Status:  0 Running / 4 Waiting / 0 Succeeded / 0 Failed
Pod Template:
  Labels:  name=busybox-pod
  Containers:
   busybox-container:
    Image:      busybox777
    Port:       <none>
    Host Port:  <none>
    Command:
      sh
      -c
      echo Hello Kubernetes! && sleep 3600
    Environment:  <none>
    Mounts:       <none>
  Volumes:        <none>
Events:
  Type    Reason            Age    From                   Message
  ----    ------            ----   ----                   -------
  Normal  SuccessfulCreate  4m26s  replicaset-controller  Created pod: new-replica-set-2hfp5
  Normal  SuccessfulCreate  4m26s  replicaset-controller  Created pod: new-replica-set-7thx9
  Normal  SuccessfulCreate  4m26s  replicaset-controller  Created pod: new-replica-set-9whtc
  Normal  SuccessfulCreate  4m26s  replicaset-controller  Created pod: new-replica-set-vb9dg
```

Answer: busybox777

## How many PODs are READY in the `new-replica-set`?

From the command above `describe replicaset`, you can see 0 is running. 

Or you can use the command below: 

```
➜  kubectl get pods
NAME                    READY   STATUS             RESTARTS   AGE
new-replica-set-2hfp5   0/1     ErrImagePull       0          72s
new-replica-set-7thx9   0/1     ImagePullBackOff   0          72s
new-replica-set-vb9dg   0/1     ImagePullBackOff   0          72s
new-replica-set-9whtc   0/1     ImagePullBackOff   0          72s
```

Answer: 0

## Why do you think the PODs are not ready?

```
➜  kubectl describe pod new-replica-set-2hfp5
Name:         new-replica-set-2hfp5
Namespace:    default
Priority:     0
Node:         controlplane/172.25.0.50
Start Time:   Sun, 01 May 2022 03:38:35 +0000
Labels:       name=busybox-pod
Annotations:  <none>
Status:       Pending
IP:           10.42.0.9
IPs:
  IP:           10.42.0.9
Controlled By:  ReplicaSet/new-replica-set
Containers:
  busybox-container:
    Container ID:  
    Image:         busybox777
    Image ID:      
    Port:          <none>
    Host Port:     <none>
    Command:
      sh
      -c
      echo Hello Kubernetes! && sleep 3600
    State:          Waiting
      Reason:       ImagePullBackOff
    Ready:          False
    Restart Count:  0
    Environment:    <none>
    Mounts:
      /var/run/secrets/kubernetes.io/serviceaccount from kube-api-access-jhzzp (ro)
Conditions:
  Type              Status
  Initialized       True 
  Ready             False 
  ContainersReady   False 
  PodScheduled      True 
Volumes:
  kube-api-access-jhzzp:
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
  Type     Reason     Age                     From               Message
  ----     ------     ----                    ----               -------
  Normal   Scheduled  8m54s                   default-scheduler  Successfully assigned default/new-replica-set-2hfp5 to controlplane
  Normal   Pulling    7m29s (x4 over 8m54s)   kubelet            Pulling image "busybox777"
  Warning  Failed     7m28s (x4 over 8m54s)   kubelet            Failed to pull image "busybox777": rpc error: code = Unknown desc = failed to pull and unpack image "docker.io/library/busybox777:latest": failed to resolve reference "docker.io/library/busybox777:latest": pull access denied, repository does not exist or may require authorization: server message: insufficient_scope: authorization failed
  Warning  Failed     7m28s (x4 over 8m54s)   kubelet            Error: ErrImagePull
  Warning  Failed     7m4s (x6 over 8m53s)    kubelet            Error: ImagePullBackOff
  Normal   BackOff    3m50s (x20 over 8m53s)  kubelet            Back-off pulling image "busybox777"
```

From event section, we can see the error message: repository does not exist or may require authorization. Answer: The image busybox777 does not exist. 

## Delete any one of the 4 PODs.

```
➜  kubectl delete pod new-replica-set-2hfp5
pod "new-replica-set-2hfp5" deleted
```

## How many PODs exist now?

Answer: 4, because of the desired replica is set to 4 in the replicaset. 

We can confirm it:

```
➜  kubectl get pods
NAME                    READY   STATUS             RESTARTS   AGE
new-replica-set-xsn2v   0/1     ImagePullBackOff   0          21m
new-replica-set-vb9dg   0/1     ImagePullBackOff   0          32m
new-replica-set-9whtc   0/1     ImagePullBackOff   0          32m
new-replica-set-7thx9   0/1     ImagePullBackOff   0          32m
```

## Why are there still 4 PODs, even after you deleted one?

see above, this is the feature from replicaset. 

## Create a ReplicaSet using the `replicaset-definition-1.yaml` file located at `/root/`.

There is an issue with the file, so try to fix it.

```
➜  cat replicaset-definition-1.yaml
apiVersion: v1
kind: ReplicaSet
metadata:
  name: replicaset-1
spec:
  replicas: 2
  selector:
    matchLabels:
      tier: frontend
  template:
    metadata:
      labels:
        tier: frontend
    spec:
      containers:
      - name: nginx
        image: nginx
```



First, create replicaset using the file replicaset-definition-1.yaml. You see the creation error

```
➜  kubectl apply -f replicaset-definition-1.yaml 
error: unable to recognize "replicaset-definition-1.yaml": no matches for kind "ReplicaSet" in version "v1"
```

Then, fix the version to

```
apiVersion: v1
-> apiVersion: apps/v1
```

Try to create replicaset using the corrected replicaset-definition-1.yaml

```
➜  kubectl apply -f replicaset-definition-1.yaml 
replicaset.apps/replicaset-1 created
```



## Fix the issue in the `replicaset-definition-2.yaml` file and create a `ReplicaSet` using it.

This file is located at `/root/`.

```
➜  vi replicaset-definition-2.yaml
apiVersion: apps/v1
kind: ReplicaSet
metadata:
  name: replicaset-2
spec:
  replicas: 2
  selector:
    matchLabels:
      tier: frontend
  template:
    metadata:
      labels:
        tier: nginx
    spec:
      containers:
      - name: nginx
        image: nginx

```

Correction - change labels for template

```
template:
    metadata:
      labels:
        tier: frontend
```

Create

```
➜  kubectl apply -f replicaset-definition-2.yaml 
replicaset.apps/replicaset-2 created
```



## Delete the two newly created ReplicaSets - `replicaset-1` and `replicaset-2`

Get all the replicasets:

```
➜  kubectl get replicasets
NAME              DESIRED   CURRENT   READY   AGE
new-replica-set   4         4         0       55m
replicaset-1      2         2         2       9m51s
replicaset-2      2         2         2       3m7s
```

Delete replicaset1

```
➜  kubectl delete replicaset replicaset-1 
replicaset.apps "replicaset-1" deleted
```

Delete replicaset2

```
➜  kubectl delete replicaset replicaset-2
replicaset.apps "replicaset-2" deleted
```

## Fix the original replica set `new-replica-set` to use the correct `busybox` image.

Either delete and recreate the ReplicaSet or Update the existing ReplicaSet and then delete all PODs, so new ones with the correct image will be created.

**Method 1: delete replica set** 

It does not work, because I cannot find the yaml file for replicaset new-replica-set. But I can confirm that **all pods created by replicaset are going to be deleted if replica set has been deleted**. 

```
➜  kubectl delete replicaset new-replica-set
replicaset.apps "new-replica-set" deleted
```

Check if any pods exist:

```
➜  kubectl get pods
No resources found in default namespace.
```

But I cannot find the yaml file for 

```
 ➜  ls
replicaset-definition-1.yaml  replicaset-definition-2.yaml  sample.yaml
```

sample.yaml is empty

```
 ➜  ls -la
total 56
drwx------    1 root     root          4096 May  1 04:57 .
drwxr-xr-x    1 root     root          4096 May  1 04:37 ..
-rw-r--r--    1 root     root           589 May  1 04:37 .bash_profile
-rw-r--r--    1 root     root           263 Apr 20 17:07 .bashrc
drwxr-xr-x    3 root     root          4096 May  1 04:42 .cache
drwxr-xr-x    1 root     root          4096 Apr 20 17:07 .config
drwxr-x---    3 root     root          4096 May  1 04:37 .kube
drwx------    2 root     root          4096 May  1 04:42 .ssh
drwxr-xr-x    4 root     root          4096 May  1 04:37 .vim
-rw-------    1 root     root          1867 May  1 04:57 .viminfo
-rw-r--r--    1 root     root           111 May  1 04:37 .vimrc
-rw-rw-rw-    1 root     root           271 May  1 04:45 replicaset-definition-1.yaml
-rw-rw-rw-    1 root     root           271 May  1 04:47 replicaset-definition-2.yaml
-rw-rw-rw-    1 root     root             0 May  1 04:57 sample.yaml
```

So I can only update the existing ReplicaSet

**Method 2: Update the existing ReplicaSet**

```
➜  kubectl edit replicaset new-replica-set
```

Change the container image `busybox777` to `busybox`

```
apiVersion: apps/v1
kind: ReplicaSet
metadata:
  creationTimestamp: "2022-05-01T05:15:57Z"
  generation: 1
  name: new-replica-set
  namespace: default
  resourceVersion: "943"
  uid: 4678b997-18ec-4186-a480-4fa39cc41530
spec:
  replicas: 4
  selector:
    matchLabels:
      name: busybox-pod
  template:
    metadata:
      creationTimestamp: null
      labels:
        name: busybox-pod
    spec:
      containers:
      - command:
        - sh
        - -c
        - echo Hello Kubernetes! && sleep 3600
        image: busybox777
        imagePullPolicy: Always
        name: busybox-container
        resources: {}
        terminationMessagePath: /dev/termination-log
        terminationMessagePolicy: File
      dnsPolicy: ClusterFirst
      restartPolicy: Always
      schedulerName: default-scheduler
      securityContext: {}
      terminationGracePeriodSeconds: 30
status:
  fullyLabeledReplicas: 4
  observedGeneration: 1
  replicas: 4
```

Result

```
➜  kubectl edit replicaset new-replica-set
replicaset.apps/new-replica-set edited
```

Then delete all 4 pods one by one

```
 ➜  kubectl get pods
NAME                    READY   STATUS             RESTARTS   AGE
new-replica-set-xp85b   0/1     ImagePullBackOff   0          11m
new-replica-set-fq62g   0/1     ImagePullBackOff   0          12m
new-replica-set-jvvdr   0/1     ImagePullBackOff   0          12m
new-replica-set-dqwjb   0/1     ImagePullBackOff   0          12m

 ➜  kubectl delete pod new-replica-set-xp85b
pod "new-replica-set-xp85b" deleted

➜  kubectl delete pod new-replica-set-fq62g
pod "new-replica-set-fq62g" deleted

➜  kubectl delete pod new-replica-set-jvvdr
pod "new-replica-set-jvvdr" deleted

➜  kubectl delete pod new-replica-set-dqwjb
pod "new-replica-set-dqwjb" deleted
```

Check the pods again, you can see new pods have been created. All pods are running.

```
➜  kubectl get pods
NAME                    READY   STATUS    RESTARTS   AGE
new-replica-set-v4pkh   1/1     Running   0          95s
new-replica-set-4wnfs   1/1     Running   0          75s
new-replica-set-hsrnc   1/1     Running   0          56s
new-replica-set-s5nl9   1/1     Running   0          40s
```



## Scale the ReplicaSet to 5 PODs.

Use `kubectl scale` command or edit the replicaset using `kubectl edit replicaset`.

```
➜  kubectl scale replicaset --replicas=5 new-replica-set
replicaset.apps/new-replica-set scaled
```

Confirm the number of pods:

```
 ➜  kubectl get pods
NAME                    READY   STATUS    RESTARTS   AGE
new-replica-set-v4pkh   1/1     Running   0          9m10s
new-replica-set-4wnfs   1/1     Running   0          8m50s
new-replica-set-hsrnc   1/1     Running   0          8m31s
new-replica-set-s5nl9   1/1     Running   0          8m15s
new-replica-set-hmrgt   1/1     Running   0          91s
```



## Now scale the ReplicaSet down to 2 PODs.

Use the `kubectl scale` command or edit the replicaset using `kubectl edit replicaset`.

```
➜ kubectl scale replicaset --replicas=2 new-replica-set
```

Confirm the number of pods, you can see 3 pods are terminating:

```
➜  kubectl get pods
NAME                    READY   STATUS        RESTARTS   AGE
new-replica-set-v4pkh   1/1     Running       0          10m
new-replica-set-4wnfs   1/1     Running       0          9m52s
new-replica-set-hmrgt   1/1     Terminating   0          2m33s
new-replica-set-s5nl9   1/1     Terminating   0          9m17s
new-replica-set-hsrnc   1/1     Terminating   0          9m33s
```

After 1 minute, check the pods again, 3 pods have been terminated. 

```
➜  kubectl get pods
NAME                    READY   STATUS    RESTARTS   AGE
new-replica-set-v4pkh   1/1     Running   0          10m
new-replica-set-4wnfs   1/1     Running   0          10m
```



Reference:

https://linuxize.com/post/how-to-save-file-in-vim-quit-editor/
