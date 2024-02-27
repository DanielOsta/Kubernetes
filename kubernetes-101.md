# Kubernetes

Getting started with Kubernetes using "Kubernetes 101" from Jeff Geerling


## Getting Started

### Install
Install [Minikube](https://minikube.sigs.k8s.io/docs/) and [kubectl](https://kubernetes.io/docs/tasks/tools/install-kubectl/) using [binenv](https://github.com/devops-works/binenv)
- `binenv install minikube`
- `binenv install kubectl`

### Basic commands
Start a Minikube cluster  
`minikube start`

Get all minikube services  
`minikube service list`

Get the cluster's state  
`kubectl get nodes`

Deploy a application to the cluster  
`kubectl create deployment hello-k8s --image=geerlingguy/kube101:intro`

Expose the port of the application  
`kubectl expose deployment hello-k8s --type=NodePort --port=80`

Access the application via the browser:  
`minikube service hello-k8s`

Stop/Delete the cluster:  
`minikube stop` / `minikube delete`

Check the deployment status of the cluster:  
`watch kubectl get deployment hello-go`

Check the pod status:  
`kubectl get pod -l app=hello-go`

Get pod details:
`kubectl describe pod -l app=hello-go`
-> Cannot pull!

Create secret for the Docker Registry:  
`kubectl create secret docker-registry regcred \
--docker-username=[username] \
--docker-password=[TOKEN GOES HERE] \
--docker-email=[email]`

Then edit the Kubernetes config:  
`kubectl edit deployment hello-go`



```yaml
spec:
    ...
    template:
        ...
        spec:
        imagePullSecrets:
        - name: regcred
        containers:
        - image: daniosta/hello-go:latest # I created my own docker image for this
```

Check the deployment:  
`kubectl get deployment hello-go`

Get the logs of the whole cluster:  
`kubectl logs -f -l app=hello-go --prefix=true
`

Change the image via the CLI (also possible with `kubectl edit`):  
`kubectl set image deployment/hello-go kube101-go=geerlingguy/kube101:hello-go-v2`

Undo the latest command:  
`kubectl rollout undo deployment hello-go`

### Using Helm:

Add Bitnami's Helm Chart repository:
`helm repo add bitnami https://charts.bitnami.com/bitnami`

Enable Loadbalancer for Minikube:
`minikube tunnel`


Install Drupal in your default namespace with the release name 'mysite'  
`helm install mysite bitnami/drupal`
Uninstall it  
`helm uninstall mysite`

Deploying Drupal Kubernetes Manifests

Create a namespace for the Drupal site.  
`kubectl create namespace drupal`  
Create the MySQL (MariaDB) Deployment.  
`kubectl apply -f mariadb.yml`  
Create the Drupal (Apache + PHP) Deployment.  
`kubectl apply -f drupal.yml`  
You can then observe the status of the deployments:  
`kubectl get deployments -n drupal -w`  

### Working in Namespaces
Set current namespace:  
`kubectl config set-context --current --namespace=drupal`  
Get current namespace:  
`kubectl config view | grep namespace:`  
Set Namespace back to default:  
`kubectl config set-context --current --namespace=""`  

Open Url with "Minikube":  
`minikube service -n drupal drupal`
With other clusters:  
`kubectl get service -n drupal drupal`  
Get more information of the nodes:  
`kubectl get nodes -o wide`  

Get logs:  
`kubectl logs -f -n drupal -l app=drupal`

**Cleaning up:**  
This will delete the whole namespace and everything inside:  
`kubectl delete namespace drupal`


### ToDo Chapter 5 (page 61)
