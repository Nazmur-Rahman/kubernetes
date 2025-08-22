
## Deploying a Kubernetes Cluster with Azure Kubernetes Service (AKS)

**🎯 Objectives:**
* Create a resource group in Azure.
* Deploy an AKS cluster with 2 worker nodes.
* Connect to the cluster and verify node status.


### Task 1: Create a Resource Group
* Open Azure Cloud Shell
* Click the Cloud Shell icon (🖥️ >_) in the top-right corner.
* Choose Bash if prompted.

Run the following command:
```
az group create --name K8S-RG --location centralus
```

* --name → Resource Group name (e.g., K8S-RG)

* --location → Region (e.g., centralus, eastus, etc.)

✅ Your resource group will be ready in a few seconds.

Verify the resource group was created:
```
az group list -o table
```
✅ You should see your new resource group in the list.


### Task 2: Deploy the AKS Cluster

Set environment variables:
```
RESOURCE_GROUP="K8S-RG"
CLUSTER_NAME="aks-cluster"
LOCATION="centralus"
```
Create the cluster with 2 worker nodes:
```
az aks create  --resource-group $RESOURCE_GROUP  --name $CLUSTER_NAME --node-count 2 --node-vm-size Standard_DS2_v2 --generate-ssh-keys
```
✅ This may take several minutes. Once completed, you’ll see details such as cluster FQDN, node pool info, and kubeConfig.

#### Task 3: Connect to the Cluster

List all clusters in your subscription:
```
az aks list -o table
```

Get cluster credentials (merge into your local kubeconfig):
```
az aks get-credentials -n $CLUSTER_NAME -g $RESOURCE_GROUP
```
### Task 4: Verify the Cluster

Check the worker nodes:
```
kubectl get nodes
```
✅ You should see 2 nodes in Ready state.

### Task 5: Delete Cluster
```
az aks delete  --name <cluster-name> --resource-group <resource-group-name>
```
🔻 This command deletes:
•	The control plane of the AKS cluster
•	Associated Azure resources (e.g., load balancers, NSGs, disks)
•	AKS-generated node resource group (if managed by AKS)
📝 The --yes flag skips the confirmation prompt.

Run the following command to delete resource group
```
az group delete --name <resource-group-name> --yes --no-wait
```
⚠️ This will delete all resources inside <resource-group>, not just AKS.
