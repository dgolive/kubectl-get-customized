K8S - SORT COMMANDS

###### get pods sort by NAME
kubectl get pods --sort-by=.metadata.name


###### get pods sort by AGE
kubectl get pods --sort-by=.status.startTime 

###### get deployments with custom-columns
kubectl get deploy -o custom-columns=DEPLOYMENT:.metadata.name,CONTAINER_IMAGE:.spec.template.spec.containers[].image,READY_REPLICAS:.status.readyReplicas,NAMESPACE:.metadata.namespace --sort-by=.metadata.name
DEPLOYMENT   CONTAINER_IMAGE   READY_REPLICAS   NAMESPACE

###### get pods sort-by NODE NAME

kubectl get po --sort-by=spec.nodeName -o wide


###### checking pv sort-by capacity

 kg pv --sort-by=spec.capacity.storage
NAME       CAPACITY   ACCESS MODES   RECLAIM POLICY   STATUS      CLAIM   STORAGECLASS   REASON   AGE
pv-log-4   40Mi       RWX            Retain           Available                                   27m
pv-log-1   100Mi      RWX            Retain           Available                                   27m
pv-log-2   200Mi      RWX            Retain           Available                                   27m
pv-log-3   300Mi      RWX            Retain           Available                                   27m



###### checking pv sort-by capacity with custom-columns

kg pv --sort-by=spec.capacity.storage -o custom-columns=NAME:.metadata.name,CAPACITY:.spec.capacity.storage --sort-by=spec.capacity.storage
NAME       CAPACITY
pv-log-4   40Mi
pv-log-1   100Mi
pv-log-2   200Mi
pv-log-3   300Mi


###### Use a JSON PATH query to identify the context configured for the aws-user in the my-kube-config context file and store the result in /opt/outputs/aws-context-name.

kubectl config view --kubeconfig=my-kube-config -o jsonpath="{.contexts[?(@.context.user=='aws-user')].name}" > /opt/outputs/aws-context-name


###### looking for pods without limits and requests 

kg po -A -o custom-columns=NAMESPACE:metadata.namespace,NAME:metadata.name,LIMITS-REQUESTS:spec.containers[*].resources |grep -v kube-system
NAMESPACE        NAME                                                        LIMITS-REQUESTS
default          app1-6ffdd6cd84-2f4dr                                       map[limits:map[cpu:500m memory:128Mi] requests:map[cpu:250m memory:64Mi]]
default          app1-6ffdd6cd84-vms6d                                       map[limits:map[cpu:500m memory:128Mi] requests:map[cpu:250m memory:64Mi]]
default          app2-d4d554b75-5pz44                                        map[]
default          app2-d4d554b75-7x696                                        map[]



### LOGS


###### all pod logs
kubectl  logs -n kong -c ingress-controller -l app.kubernetes.io/instance=kong -f --max-log-requests=6

###### using tail
kubectl logs --tail=30 -n kube-system pod/kube-apiserve



###### DNS debbuing resolution
kubectl apply -f https://k8s.io/examples/admin/dns/dnsutils.yaml
kubectl exec -i -t dnsutils -- nslookup kubernetes.default

ref.: https://kubernetes.io/docs/tasks/administer-cluster/dns-debugging-resolution/


#### kubernetes cheat sheet

###### expand pvc
- kubectl patch pvc pvc-name --patch '{ "spec": { "resources": { "requests": { "storage": "120Mi" }}}}'


###### pvc or pv in terminating state:
- kubectl get pvc | tail -n+2 | awk '{print $1}' | xargs -I{} kubectl patch pvc {} -p '{"metadata":{"finalizers": null}}'
- kubectl get pv | tail -n+2 | awk '{print $1}' | xargs -I{} kubectl patch pv {} -p '{"metadata":{"finalizers": null}}'

###### run curl test temporarily
- kubectl run --rm helloworld --image=yauritux/busybox-curl -it

###### run wget test temporarily
- kubectl run --rm helloworld --image=busybox -it

###### run nginx deployment with 2 replicas
- kubectl run --rm helloworld-web --image=nginx --replicas=2 --port=80

###### run nginx pod and expose it
- kubectl run hellowolrd-web --image=nginx
