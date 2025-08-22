**🔹 Why do we need PV and PVC?**

By default, Kubernetes storage (like emptyDir or hostPath) is temporary:

* If a Pod crashes or restarts, the data is lost.
* If a Pod is rescheduled to another node, local storage does not move with it.

👉 To solve this, Kubernetes provides Persistent Volumes (PV) and Persistent Volume Claims (PVC)

**PersistentVolume (PV):**

* A piece of storage in the cluster (like a disk, NFS share, cloud volume).
* Created and managed by cluster administrators.
* Defines size, access mode, and backend type (e.g., NFS, AWS EBS, Azure Disk, hostPath).



**PersistentVolumeClaim (PVC):**

* A request for storage by a deployment/Pod.
* Kubernetes automatically matches a PVC to a suitable PV.
* This way, users don’t need to worry about where or how the storage is provided.

**Access Modes:**

* ReadWriteOnce (RWO): One Pod can read/write.
* ReadOnlyMany (ROX): Many Pods can read.
* ReadWriteMany (RWX): Many Pods can read/write.

----------------------------------------------------------------------------------------------------------------------------------------
## Static Provisioning

### Task 1: Get Node Label and Create Custom Index.html on Node
View worker nodes and their labels
```
kubectl get nodes --show-labels | grep "kubernetes.io/hostname"
```
Pick a worker node (e.g., *kworker1*) and note its *kubernetes.io/hostname* label.

SSH into that worker node: 
```
ssh <UserName>@<Node-IP>
```
i.e ssh labuser@172.31.29.170


Switch to root and run the following commands
```
sudo su
```
Create directory with custom *index.html* file.
```
mkdir /pvdir
```
```
echo Hello World! > /pvdir/index.html
```
This directory will be mounted into the Pod later.

### On the Master Node (kmaster vm)
### Task 2: Create a Local Persistent Volume
```
vi pv-volume.yaml
```

Add the given content, by pressing `INSERT`

```yaml
kind: PersistentVolume
apiVersion: v1
metadata:
  name: pv-volume
spec:
  storageClassName: manual
  capacity:
    storage: 1Gi
  accessModes:
    - ReadWriteMany
  hostPath:
    path: "/pvdir"
```
save the file using `ESCAPE + :wq!`

Apply the PV:
```
kubectl apply -f pv-volume.yaml
```
Verify PV:
```
kubectl get pv
```
```
kubectl describe pv pv-volume
```

### Task 3: Create a PV Claim
Now request storage from the PV.
```
vi pv-claim.yaml
```
Add the given content, by pressing `INSERT`

```yaml
kind: PersistentVolumeClaim
apiVersion: v1
metadata:
  name: pv-claim
spec:
  storageClassName: manual  #Must match PV’s storageClassName
  accessModes:
  - ReadWriteMany           #Must match PV’s access mode
  resources:
    requests:
      storage: 1Gi          #Request 1Gi (must be ≤ PV size)
```
save the file using `ESCAPE + :wq!`

Apply PVC:
```
kubectl apply -f pv-claim.yaml
```

Verify PVC is Bound:
```
kubectl get pvc
```

### Task 4: Create nginx Pod with NodeSelector
Now create a Pod that uses this PVC and mounts it into nginx.
```
vi pv-pod.yaml
```

Add the given content, by pressing `INSERT`

```yaml
kind: Pod
apiVersion: v1
metadata:
  name: pv-pod
spec:
  volumes:
    - name: pv-storage
      persistentVolumeClaim:
        claimName: pv-claim
  containers:
     - name: pv-container
       image: nginx
       ports:
          - containerPort: 80
            name: "http-server"
       volumeMounts:
          - mountPath: "/usr/share/nginx/html"
            name: pv-storage
  nodeSelector:
    kubernetes.io/hostname: kworker1
```
save the file using `ESCAPE + :wq!`


Apply the Pod yaml created in the previous step
```
kubectl apply -f pv-pod.yaml
```
View Pod details and see that is created on the required node
```
kubectl get pods -o wide
```
Access shell on a container running in your Pod
```
kubectl exec -it pv-pod -- /bin/bash
```
```
exit
```

### Task 5: Cleanup
delete the resources created in this lab.
```
kubectl delete -f pv-pod.yaml
```
```
kubectl delete -f pv-claim.yaml
```
```
kubectl delete -f pv-volume.yaml
```

