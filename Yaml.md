vi httpd-pod.yaml 

kubectl apply -f httpd-pod.yaml 

vi httpd-pod.yaml 

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: httpd-pod-1
  labels:
    env: prod 
    type: front-end
    app: httpd-ws
spec:
  nodeName: kworker2
  volumes:
  - name: hp-volume
    hostPath:
      path: /data
  containers:
  - name: httpd-container
    image: httpd
    ports:
    - containerPort: 80
    volumeMounts:
    - name: hp-volume
      mountPath: /mnt/docs
```

kubectl apply -f httpd-pod.yaml 

kubectl exec -it httpd-pod-1 -- bash

exit

--------------------------------------------------------

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: security-context-new
spec:
  securityContext:
    fsGroup: 2000     # common group of files
  volumes:
  - name: sec-ctx-vol
    emptyDir: {}
  containers:
  - name: ctr-1
    image: busybox:1.28
    command: [ "sh", "-c", "sleep 1h" ]
    volumeMounts:
    - name: sec-ctx-vol
      mountPath: /data/demo
    securityContext:
      runAsUser: 1010   #UID
      runAsGroup: 3010  #GID
  - name: ctr-2
    image: busybox:1.28
    command: [ "sh", "-c", "sleep 1h" ]
    volumeMounts:
    - name: sec-ctx-vol
      mountPath: /docs/demo
    securityContext:
      runAsUser: 1020   #UID
      runAsGroup: 3020  #GID
      capabilities:
        add: ["NET_ADMIN", "SYS_TIME", "SYS_ADMIN"]
```
-------------------------------------------------------------------------------------------------------

```yaml
apiVersion: networking.k8s.io/v1
kind: NetworkPolicy
metadata:
  namespace: devops
  name: backend-policy
spec:
  podSelector:
    matchLabels:
      run: ng-pod
  ingress: 
  - from:
    - namespaceSelector:
        matchLabels:
          project: myproject
      podSelector:
        matchLabels:
         role: frontend
```


kubectl -n finance run pod4 --image centos:7 --labels role=frontend -- sleep 5000
kubectl -n finance get pod --show-labels
kubectl -n finance exec -it pod4 -- bash
