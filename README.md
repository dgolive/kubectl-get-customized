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
