# redis-kube

Sample code showing how to deploy Redis with persistent storage

## Manually deploy Redis
Files are in the `basic` directory


Create a namespace 
```
kubectl create ns redis
```

Define a storage class 
(Tell Kubernetes that we're going to store information on disk and it should _not_ be deleted between runs)
```
kubectl apply -f sc.yaml
```

Create the config map (Key/Value pairs mounted as a file)
```
kubectl apply -n redis -f redis-config.yaml
```

Validate it as:
```
kubectl get configmap -n redis
```

Deploy code as a stateful set
(Restart pods if they crash, use persistant storage)
```
kubectl apply -n redis -f redis-statefulset.yaml
kubectl get pods -n redis
```

Expose Redis as a service on port 32379
```
kubectl apply -n redis -f redis-service.yaml
```

Test with the redis command line utility
```
redis-cli -p 32379 
127.0.0.1:32379> set sample "Hello World"
OK
127.0.0.1:32379> exit
igg@AMER-igg2 k8s % kubectl get pods -n redis
NAME      READY   STATUS    RESTARTS   AGE
redis-0   1/1     Running   0          31m
igg@AMER-igg2 k8s % kubectl delete pod redis-0 -n redis
pod "redis-0" deleted
igg@AMER-igg2 k8s % kubectl get pods -n redis
NAME      READY   STATUS    RESTARTS   AGE
redis-0   1/1     Running   0          7s
igg@AMER-igg2 k8s % redis-cli -p 32379        
127.0.0.1:32379> keys *
1) "sample"
127.0.0.1:32379> get sample
"Hello World"
127.0.0.1:32379> exit
```
