# Lab - namespace

**Frequently used commands**

```
kubectl get ns
kubectl create ns dev-ns
kubectl get pods --namespace=dev
kubectl -n research get pods --no-headers
kubectl set-context $(kubectl config current-context) --namespace=dev
kubectl get pods --all-namespaces
```

Note: `ns` and `n` is the short for `namespaces`

## How many Namespaces exist on the system?

**Best solution:**

```
➜  kubectl get ns --no-headers | wc -l
10
```

If we don't add `--no-header`, then we will also count the header line, so 11 instead of 10

```
➜  kubectl get ns | wc -l
11
```

**Alternative solution:**

```
➜  kubectl get ns
NAME              STATUS   AGE
default           Active   8m30s
kube-system       Active   8m30s
kube-public       Active   8m30s
kube-node-lease   Active   8m30s
finance           Active   3m10s
marketing         Active   3m10s
dev               Active   3m10s
prod              Active   3m10s
manufacturing     Active   3m10s
research          Active   3m10s
```

Answer: 10

## How many pods exist in the `research` namespace?

**Best solution:**

```
➜  kubectl -n research get pods --no-headers
dna-2   0/1   CrashLoopBackOff   8 (3m17s ago)   19m
dna-1   0/1   CrashLoopBackOff   8 (3m1s ago)    19m

```

**Alternative solution:**

```
➜  kubectl get pods --namespace=research 
NAME    READY   STATUS             RESTARTS      AGE
dna-2   0/1     CrashLoopBackOff   5 (68s ago)   4m22s
dna-1   0/1     CrashLoopBackOff   5 (64s ago)   4m22s
```

Answer: 2

## Create a POD in the `finance` namespace.

**Solution 1:**

Create a pod definition yaml file

```
kubectl run redis --image=redit --dry-run=client -o yaml>redis-pod.yaml
```

Add namespace to `metadata` of  redis-pod.yaml file

```
apiVersion: v1
kind: Pod
metadata:
  creationTimestamp: null
  labels:
    run: redis
  name: redis
  namespace: finance
spec:
  containers:
  - image: redit
    name: redis
    resources: {}
  dnsPolicy: ClusterFirst
  restartPolicy: Always
status: {}
```

Create pod using redis-pod.yaml file

```
➜  kubectl apply -f redis-pod.yaml 
pod/redis created
```

Verify if the pod is created

```
➜  kubectl -n finance get pod redis
NAME    READY   STATUS             RESTARTS   AGE
redis   0/1     ImagePullBackOff   0          97s
```

delete pod so we can do solution 2:

```
kubectl -n finance delete pod redis
pod "redis" deleted
```



**Solution 2:**

Use the spec given below.

- Name: redis
- Image Name: redis

```
➜  kubectl run redis --image=redis --namespace=finance
pod/redis created
```

## Which namespace has the `blue` pod in it?

```
kubectl get pod --all-namespaces | grep blue
NAMESPACE       NAME                                      READY   STATUS             
marketing       blue                                      1/1     Running            0              8m20s
```

Answer: marketing

## Access the Blue web application using the link above your terminal

From the UI you can ping other services

## What DNS name should the Blue application use to access the database `db-service` in its own namespace - `marketing`.

You can try it in the web application UI. Use port `6379`.

In this case, we try to reach the service in the same namespace. We can just use service name as DNS name. 

hostname: db-serive

Hostpot: 6379

## What DNS name should the Blue application use to access the database 'db-service' in the 'dev' namespace

You can try it in the web application UI. Use port 6379.

In this case, we try to reach the service in the different namespace. 

hostname: db-service.dev.svc.cluster.local

Hostpot: 6379
