# Lab - Configmaps

Frequent used commands:

```
kubectl get configmaps
kubectl describe cm <config-name>
kubectl create cm <config-name> --from-liberal=<key>=<value>
```

cm is the short form of configmap.

## How many PODs exist on the system?

in the current(default) namespace

```
➜  kubectl get pods
NAME           READY   STATUS    RESTARTS   AGE
webapp-color   1/1     Running   0          44s
```

Answer: 1

## What is the environment variable name set on the container in the pod?

```
➜  kubectl describe pod webapp-color 
Name:         webapp-color
Namespace:    default
Priority:     0
Node:         controlplane/172.25.0.40
Start Time:   Tue, 03 May 2022 04:44:01 +0000
Labels:       name=webapp-color
Annotations:  <none>
Status:       Running
IP:           10.42.0.9
IPs:
  IP:  10.42.0.9
Containers:
  webapp-color:
    Container ID:   containerd://7e1dbd6cad3ab5f723a6ba65eb54c9272f8e0ee1c53fbc807cd3faa4aa3f2a95
    Image:          kodekloud/webapp-color
    Image ID:       docker.io/kodekloud/webapp-color@sha256:99c3821ea49b89c7a22d3eebab5c2e1ec651452e7675af243485034a72eb1423
    Port:           <none>
    Host Port:      <none>
    State:          Running
      Started:      Tue, 03 May 2022 04:44:12 +0000
    Ready:          True
    Restart Count:  0
    Environment:
      APP_COLOR:  pink
    Mounts:
      /var/run/secrets/kubernetes.io/serviceaccount from kube-api-access-fht2k (ro)
Conditions:
  Type              Status
  Initialized       True 
  Ready             True 
  ContainersReady   True 
  PodScheduled      True 
Volumes:
  kube-api-access-fht2k:
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
  Normal  Scheduled  5m27s  default-scheduler  Successfully assigned default/webapp-color to controlplane
  Normal  Pulling    5m25s  kubelet            Pulling image "kodekloud/webapp-color"
  Normal  Pulled     5m17s  kubelet            Successfully pulled image "kodekloud/webapp-color" in 7.469608411s
  Normal  Created    5m17s  kubelet            Created container webapp-color
  Normal  Started    5m17s  kubelet            Started container webapp-color
```

Answer: APP_COLOR

## What is the value set on the environment variable `APP_COLOR` on the container in the pod?

Answer: pink

## View the web application UI by clicking on the `Webapp Color` Tab above your terminal.

This is located on the right side.

## Update the environment variable on the POD to display a `green` background

Note: Delete and recreate the POD. Only make the necessary changes. Do not modify the name of the Pod.

- Pod Name: webapp-color
- Label Name: webapp-color
- Env: APP_COLOR=green

You cannot edit the pod directly - Wrong

```
kubectl edit pod webapp-color --Env=APP_Color=green
```

Solution:

Create a pod definition file for the existing pod

```
kubectl get pod webapp-color -o yaml > webapp-color.yaml
```

Delete the current pod

```
➜  kubectl delete pod webapp-color
pod "webapp-color" deleted
```

Update the environment variable value from pink to green for variable name APP_COLOR of webapp-color.yaml

```
➜  vi webapp-color.yaml
apiVersion: v1
kind: Pod
metadata:
  creationTimestamp: "2022-05-03T04:44:01Z"
  labels:
    name: webapp-color
  name: webapp-color
  namespace: default
  resourceVersion: "824"
  uid: 8b5fccf5-553b-483d-ad2e-4cf4b4a68d24
spec:
  containers:
  - env:
    - name: APP_COLOR
      value: pink
    image: kodekloud/webapp-color
    imagePullPolicy: Always
    name: webapp-color
    resources: {}
    terminationMessagePath: /dev/termination-log
    terminationMessagePolicy: File
    volumeMounts:
    ...
    ...
```

Create the pod

```
➜  kubectl create -f webapp-color.yaml 
pod/webapp-color created
```

Refresh the web application UI, the screen color is green. 

## View the changes to the web application UI by clicking on the `Webapp Color` Tab above your terminal.

If you already have it open, simply refresh the browser.

## How many `ConfigMaps` exists in the `default` namespace?

```
➜  kubectl get configmaps
NAME               DATA   AGE
kube-root-ca.crt   1      39m
db-config          3      27s
```

Answer: 2

## Identify the database host from the config map `db-config`

```
➜  kubectl describe configmap db-config
Name:         db-config
Namespace:    default
Labels:       <none>
Annotations:  <none>

Data
====
DB_PORT:
----
3306
DB_HOST:
----
SQL01.example.com
DB_NAME:
----
SQL01

BinaryData
====

Events:  <none>
```

Answer: SQL01.example.com

## Create a new ConfigMap for the `webapp-color` POD. Use the spec given below.

- ConfigName Name: webapp-config-map
- Data: APP_COLOR=darkblue

```
➜  kubectl create configmap webapp-config-map --from-literal=APP_COLOR=darkblue
configmap/webapp-config-map created
```

## Update the environment variable on the POD to use the newly created ConfigMap

Note: Delete and recreate the POD. Only make the necessary changes. Do not modify the name of the Pod.

Original file

```
apiVersion: v1
kind: Pod
metadata:
  creationTimestamp: "2022-05-03T04:44:01Z"
  labels:
    name: webapp-color
  name: webapp-color
  namespace: default
  resourceVersion: "824"
  uid: 8b5fccf5-553b-483d-ad2e-4cf4b4a68d24
spec:
  containers:
  - env:
    - name: APP_COLOR
      value: green
    image: kodekloud/webapp-color
    imagePullPolicy: Always
    name: webapp-color
    resources: {}
    terminationMessagePath: /dev/termination-log
    terminationMessagePolicy: File
    volumeMounts:
```

Updated environment variable in the file

```
apiVersion: v1
kind: Pod
metadata:
  creationTimestamp: "2022-05-03T04:44:01Z"
  labels:
    name: webapp-color
  name: webapp-color
  namespace: default
  resourceVersion: "824"
  uid: 8b5fccf5-553b-483d-ad2e-4cf4b4a68d24
spec:
  containers:
  - envFrom:
    - configMapRef
          name: webapp-config-map
    image: kodekloud/webapp-color
    imagePullPolicy: Always
    name: webapp-color
    resources: {}
    terminationMessagePath: /dev/termination-log
    terminationMessagePolicy: File
    volumeMounts:
```

Name or env under container why no indent? name is much more indent than 2

What pod can be edited?

array

