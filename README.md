# kubectl get customized


##### get nodes with custom-colums
```
kubectl get nodes -o custom-columns='NAME:metadata.name,'\
'STATUS:status.conditions[3].type,'\
'VERSION:status.nodeInfo.kubeletVersion,'\
'OS-IMAGE:status.nodeInfo.osImage,'\
'CONTAINER-RUNTIME:status.nodeInfo.containerRuntimeVersion'
```
```
NAME                                       STATUS   VERSION               OS-IMAGE                         CONTAINER-RUNTIME
ip-10-0-1-121.us-east-2.compute.internal   Ready    v1.21.5-eks-bc4871b   Windows Server 2019 Datacenter   docker://20.10.9
```

### Sorting pods by name


##### get pods sort by NAME
```
kubectl get pods --sort-by=.metadata.name
```

##### get pods sort by AGE
```
kubectl get pods --sort-by=.status.startTime 
```

### Customizing the get resource and sorting
##### get deployments with custom-columns, like: DEPLOYMENT   CONTAINER_IMAGE   READY_REPLICAS   NAMESPACE 
```
kubectl get deploy -o custom-columns='DEPLOYMENT:.metadata.name,'\
'CONTAINER_IMAGE:.spec.template.spec.containers[].image,'\
'READY_REPLICAS:.status.readyReplicas,'\
'NAMESPACE:.metadata.namespace'\
'--sort-by=.metadata.name'
```

##### get pods sort-by NODE NAME

kubectl get pods --sort-by=spec.nodeName -o wide




##### checking pv sort-by capacity
```
 kg pv --sort-by=spec.capacity.storage
 ```
 ```
NAME       CAPACITY   ACCESS MODES   RECLAIM POLICY   STATUS      CLAIM   STORAGECLASS   REASON   AGE
pv-log-4   40Mi       RWX            Retain           Available                                   27m
pv-log-1   100Mi      RWX            Retain           Available                                   27m
pv-log-2   200Mi      RWX            Retain           Available                                   27m
pv-log-3   300Mi      RWX            Retain           Available                                   27m
```

##### checking pv sort-by capacity with custom-columns
```
kubectl get pv --sort-by=spec.capacity.storage -o custom-columns=NAME:.metadata.name,CAPACITY:.spec.capacity.storage --sort-by=spec.capacity.storage
```
```
NAME       CAPACITY
pv-log-4   40Mi
pv-log-1   100Mi
pv-log-2   200Mi
pv-log-3   300Mi
```



##### looking for pods without limits and requests 
```
kubectl get pods -A -o custom-columns='NAMESPACE:metadata.namespace,NAME:metadata.name,LIMITS-REQUESTS:spec.containers[*].resources' |grep -v kube-system
```
#
```
NAMESPACE        NAME                                                        LIMITS-REQUESTS
default          app1-6ffdd6cd84-2f4dr                                       map[limits:map[cpu:500m memory:128Mi] requests:map[cpu:250m memory:64Mi]]
default          app1-6ffdd6cd84-vms6d                                       map[limits:map[cpu:500m memory:128Mi] requests:map[cpu:250m memory:64Mi]]
default          app2-d4d554b75-5pz44                                        map[]
default          app2-d4d554b75-7x696                                        map[]
```



```
