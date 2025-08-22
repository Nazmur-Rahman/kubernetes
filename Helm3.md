## Helm
**Helm** is the package manager for Kubernetes.
It simplifies the deployment and management of applications inside Kubernetes clusters by packaging all necessary YAML manifests into reusable bundles called charts.

**🔑 Key Concepts**
* *Chart →* A collection of pre-configured Kubernetes resources (like Deployments, Services, PVCs, ConfigMaps).
* *Release →* A running instance of a chart inside a cluster. You can install the same chart multiple times under different release names.
* *Repository →* A collection of charts hosted online (similar to Docker Hub but for Helm charts).

**⚙️ Why Use Helm?**
* Deploy complex apps (like WordPress, Jenkins, Prometheus) with a *single command*.
* Easily *upgrade* or *rollback* applications.
* Manage app *versions* consistently.
* Customize deployments using *values.yaml* without rewriting YAMLs.
* Use *community-maintained charts* for popular apps.
------------------------------------------------------------------------------------------------------------------------------------------------------------------
### Task 1: Installing Helm3 in Kubernetes
Setting up Helm on Kubernetes

Run the following command to update the packages:
```
apt update
```
Download the Helm binary for Linux 386 architecture:
```
wget https://get.helm.sh/helm-v3.11.3-linux-386.tar.gz
```
Unpack the downloaded tarball: 
```
tar -xvzf helm-v3.11.3-linux-386.tar.gz
```
Check the contents of the extracted directory to ensure that the Helm binary is present:
```
ls
```
Move the Helm executable to the /bin directory to make it accessible system-wide:
 
```
mv linux-386/helm /bin/
```
Confirm that Helm is installed correctly by checking its version:
```
helm version
```
## Task 2: Setup WordPress
To add a Helm chart repository to your local environment. Below we are adding two Helm Chart Repositories.
```
helm repo add bitnami https://charts.bitnami.com/bitnami 
```
To list the Helm chart repositories configured in your local environment.
```
helm repo list
```
To search for Helm charts related to WordPress in the configured Helm repositories
```
helm search repo wordpress
```
To install the WordPress application using the Bitnami Helm chart with the release name my-wordpress
```
helm install my-wordpress bitnami/wordpress
```
To list all the releases currently installed on your Kubernetes cluster
```
helm list
```

#### Verify that WordPress has been set up 

```
kubectl get all
```
Verify that the pods are running
```
kubectl get pods
```

Also Notice that the front end of wordpress are part of deployment
```
kubectl get deploy
```
View the services using the below commands. Make note of the EXTERNAL IP of the LoadBalancer service(If LoadBalancer is integrated with Cluster)
```
kubectl get svc
```
Change the type of wordpress service to ***NodePort***
```
kubectl edit svc wordpres
```
verify the changes
```
kubectl get svc
```
Open the browser and paste the Public Ip of the Node along with service nodePort noted on the previous step. Observe that the WordPress site is up and running
> http://<IP-of-the-Node>:<NodePort>
> i.e.. http://13.8.90.52:30288

#### Cleanup
List the current helm release and delete it
```
helm ls
```

To uninstall the WordPress application deployed using Helm with the release name my-wordpress
```
helm uninstall my-wordpress
```
```
helm ls
```
 To remove a Helm repository from your local Helm client configuration
```
helm repo remove bitnami
```
To remove Helm package manager
```
sudo apt-get remove helm
```
 
