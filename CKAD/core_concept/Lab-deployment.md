# Lab - deployment

Frequently used command

```
> kubectl apply -f deployment-definition.yml
> kubectl get deployments
> kubectl describe deployments deployment-mame
> kubectl get all
```

You can use  `kubectl get all` command to see the deployment was created, then replicaset and followed by pods that were created as part  of the deployment. 

## How many PODs exist on the system?

In the current(default) namespace.

```
➜  kubectl get pods
No resources found in default namespace.
```

Answer: 0

## How many ReplicaSets exist on the system?

In the current(default) namespace.

```
➜  kubectl get replicasets
No resources found in default namespace.
```

Answer: 0

## How many Deployments exist on the system?

In the current(default) namespace.

```
➜  kubectl get deployments
No resources found in default namespace.
```

Answer: 0

## How many Deployments exist on the system now?

We just created a Deployment! Check again!

```
➜  kubectl get deployments
NAME                  READY   UP-TO-DATE   AVAILABLE   AGE
frontend-deployment   0/4     4            0           24s
```

Answer: 1

## How many ReplicaSets exist on the system now?

```
➜  kubectl get replicasets
NAME                             DESIRED   CURRENT   READY   AGE
frontend-deployment-7fd8cdb696   4         4         0       86s
```

Answer: 1

## How many PODs exist on the system now?

```
➜  kubectl get pods
NAME                                   READY   STATUS             RESTARTS   AGE
frontend-deployment-7fd8cdb696-jqgdc   0/1     ImagePullBackOff   0          2m15s
frontend-deployment-7fd8cdb696-hqlmt   0/1     ImagePullBackOff   0          2m15s
frontend-deployment-7fd8cdb696-r5r9f   0/1     ImagePullBackOff   0          2m15s
frontend-deployment-7fd8cdb696-zksfg   0/1     ImagePullBackOff   0          2m15s
```

Answer: 4

## Out of all the existing PODs, how many are ready?

from `kubectl get deployments` command,  ready is 0/4. 

Answer: 0

## What is the image used to create the pods in the new deployment?

Method 1: best

```
 ➜  kubectl describe deployments frontend-deployment | grep -i image 
    Image:      busybox888
```

Method 2: 

Or we can get it from describe pod

```
➜  kubectl describe pod frontend-deployment-7fd8cdb696-jqgdc
Name:         frontend-deployment-7fd8cdb696-jqgdc
Namespace:    default
Priority:     0
Node:         controlplane/172.25.0.55
Start Time:   Sun, 01 May 2022 16:19:27 +0000
Labels:       name=busybox-pod
              pod-template-hash=7fd8cdb696
Annotations:  <none>
Status:       Pending
IP:           10.42.0.10
IPs:
  IP:           10.42.0.10
Controlled By:  ReplicaSet/frontend-deployment-7fd8cdb696
Containers:
  busybox-container:
    Container ID:  
    Image:         busybox888
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
      /var/run/secrets/kubernetes.io/serviceaccount from kube-api-access-hjvjn (ro)
Conditions:
  Type              Status
  Initialized       True 
  Ready             False 
  ContainersReady   False 
  PodScheduled      True 
Volumes:
  kube-api-access-hjvjn:
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
  Type     Reason     Age                    From               Message
  ----     ------     ----                   ----               -------
  Normal   Scheduled  5m                     default-scheduler  Successfully assigned default/frontend-deployment-7fd8cdb696-jqgdc to controlplane
  Normal   Pulling    3m30s (x4 over 5m)     kubelet            Pulling image "busybox888"
  Warning  Failed     3m30s (x4 over 4m59s)  kubelet            Failed to pull image "busybox888": rpc error: code = Unknown desc = failed to pull and unpack image "docker.io/library/busybox888:latest": failed to resolve reference "docker.io/library/busybox888:latest": pull access denied, repository does not exist or may require authorization: server message: insufficient_scope: authorization failed
  Warning  Failed     3m30s (x4 over 4m59s)  kubelet            Error: ErrImagePull
  Warning  Failed     3m18s (x6 over 4m59s)  kubelet            Error: ImagePullBackOff
  Normal   BackOff    3m5s (x7 over 4m59s)   kubelet            Back-off pulling image "busybox888"
```

Answer: busybox888

## Why do you think the deployment is not ready?

You can see the answer in the event section of `kubectl describe pod frontend-deployment-7fd8cdb696-jqgdc`  above. 

The image busybox888 does not exist. 

## Create a new Deployment using the `deployment-definition-1.yaml` file located at `/root/`.

There is an issue with the file, so try to fix it.

```
➜  kubectl apply -f deployment-definition-1.yaml 
Error from server (BadRequest): error when creating "deployment-definition-1.yaml": deployment in version "v1" cannot be handled as a Deployment: no kind "deployment" is registered for version "apps/v1" in scheme "k8s.io/apimachinery@v1.23.3-k3s1/pkg/runtime/scheme.go:100"
```



```
➜  vi deployment-definition-1.yaml 
```

Make the following changes: 

* kind from `deployment` to `Deployment`

* The container `busybox-container` image from `busybox888 ` to `busybox`. This one is optional. 

```
---
apiVersion: apps/v1
kind: deployment
metadata:
  name: deployment-1
spec:
  replicas: 2
  selector:
    matchLabels:
      name: busybox-pod
  template:
    metadata:
      labels:
        name: busybox-pod
    spec:
      containers:
      - name: busybox-container
        image: busybox888
        command:
        - sh
        - "-c"
        - echo Hello Kubernetes! && sleep 3600
```

## Create a new Deployment with the below attributes using your own deployment definition file.

Name: `httpd-frontend`;
Replicas: `3`;
Image: `httpd:2.4-alpine`

## Create a new Deployment with the below attributes using your own deployment definition file.

Name: `httpd-frontend`;
Replicas: `3`;
Image: `httpd:2.4-alpine`

**Method 1: **without deployment definition file

Best. 

**Create deployment, this will create one replica**

```
➜  kubectl create deployment httpd-frontend --image=httpd:2.4-alpine
deployment.apps/httpd-frontend created
```

**Then scale the replicas to 3**

```
➜  kubectl scale deployment --replicas=3 httpd-frontend
deployment.apps/httpd-frontend scaled
```

**Verify the deployment**

```
➜  kubectl get deployment httpd-frontend
NAME             READY   UP-TO-DATE   AVAILABLE   AGE
httpd-frontend   3/3     3            3           56s
```

**Method 2: **

**Create a deployment definition file**

```
➜  touch httpd-deployment-definition.yaml
```

**Then use vim to add the following content to the file**

```
➜  vi httpd-deployment-definition.yaml 
```

Content of httpd-deployment-definition.yaml 

```
---
apiVersion: apps/v1
kind: Deployment
metadata:
  name: httpd-frontend
spec:
  replicas: 3
  selector:
    matchLabels:
      name: httpd-pod
  template:
    metadata:
      labels:
        name: httpd-pod
    spec:
      containers:
      - name: httpd-container
        image: httpd:2.4-alpine
        
```

**Note:** container name and pod name must be lower case

**Create deployment using the definition file**

```
➜  kubectl apply -f httpd-deployment-definition.yaml 
deployment.apps/httpd-frontend created
```

Confirm the creation of the deploymenta;

```
 ➜  kubectl get deployments
NAME                  READY   UP-TO-DATE   AVAILABLE   AGE
frontend-deployment   0/4     4            0           49m
deployment-1          2/2     2            2           35m
httpd-frontend        3/3     3            3           12m
```

