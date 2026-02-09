# Kubernetes

Going through the Kubernetes tutorial on [kubernetes.io](kubernetes.io)

## Installatioin

```bash
brew install kubectl
brew install minikube

# Docker needs to be installed
# In the docker desktop app go to Settings -> Kubernetes -> Enable Kubernetes
minikube delete --all --purge
unset DOCKER_DEFAULT_PLATFORM
rm -rf ~/.minikube/
minikube start
```

### Terminal

```bash
# From https://kubernetes.io/docs/tasks/tools/install-kubectl-macos/#enable-shell-autocompletion
# add the following to the ~/.zshrc file:
source <(kubectl completion zsh)
```

## Getting Started

```bash
minikube start
minikube dashboard # Opens the Kubernetes dashboard
kubectl create ... # Create deployment
kubectl get deployments # View deployments
kubectl create deployment hello-node --image=registry.k8s.io/e2e-test-images/agnhost:2.39 -- /agnhost netexec --http-port=8080 # create test pod
kubectl get pods # view the pods
kubectl describe pods # describe the pods
kubectl get events # view the cluster events
kubectl config view # display the configuration
kubectl expose deployment hello-node --type=LoadBalancer --port=8080 # expose pod as a service
kubectl get services # view services
minikube service hello-node # only for minikube - to view the service

kubectl get pod,svc -n kube-system # view pods and services

# switch context
kubectl config use-context minikube
```

### Clean up

```bash
kubectl delete service hello-node
kubectl delete deployment hello-node
minikube stop
# Optional
minikube delete
```

### Minikube

```bash
minikube addons list
minikube addons enable metrics-server # enable Minikube addons
kubectl top pods # view output of `metrics-server` - could take 2min after install
minikube addons disable metrics-server
```

### `kubectl` basics

```bash
# Basic: kubectl action resource
kubectl get nodes --help

kubectl create deployment kubernetes-bootcamp --image=nginx:latest
kubectl get deployments

# Will create a proxy that will forward communications into the private network (my terminial)
kubectl proxy

# Get the pod name
export POD_NAME=$(kubectl get pods -o go-template --template '{{range .items}}{{.metadata.name}}{{"\n"}}{{end}}')
echo Name of the Pod: $POD_NAME

# Access the Pod through the proxied API
curl http://localhost:8001/api/v1/namespaces/default/pods/$POD_NAME/
```

`kubectl` commands: https://kubernetes.io/docs/reference/kubectl/

**Pods** are the atomic unit on the Kubernetes platform.
A pod can consist of multiple containers.
In case of a Node failure, identical Pods are scheduled on other available Nodes in the cluster.  
A **Node** is a worker machine in Kubernetes and can be either a virtual or a physical machine.
It can have multiple pods.  
Every Node runs at least:

- Kubelet - process responsible for communication between the K8s control plane and the Node
- A container runtime (like Docker)

### Node overview

![alt text](https://kubernetes.io/docs/tutorials/kubernetes-basics/public/images/module_03_nodes.svg)

**kubectl commands**
Most common operations can be done with this subcommands:

```bash
kubectl get # list resources
kubectl describe # show detailed information about a resource
kubectl logs # print the logs from a container in a pod
kubectl exec # execute a command on a container in a pod

kubectl exec "$POD_NAME" -- env
kubectl exec -ti $POD_NAME -- bash
```

## Expose the App Publicly

### Creating a new Service

```bash
kubectl expose deployment/kubernetes-bootcamp --type="NodePort" --port 8080
kubectl get services

# only for docker-desktop and minikube:
minikube service kubernetes-bootcamp --url
curl 127.0.0.1:<PORT>
# otherwise:
export NODE_PORT="$(kubectl get services/kubernetes-bootcamp -o go-template='{{(index .spec.ports 0).nodePort}}')"
echo "NODE_PORT=$NODE_PORT"
curl http://"$(minikube ip):$NODE_PORT"
```

### Using labels

```bash
export POD_NAME="$(kubectl get pods -o go-template --template '{{range .items}}{{.metadata.name}}{{"\n"}}{{end}}')"
echo "Name of the Pod: $POD_NAME"

kubectl label pods "$POD_NAME" version=v1 # add label
kubectl describe pods "$POD_NAME"

kubectl get pods -l version=v1
```

### Delete a service

```bash
kubectl delete service -l app=kubernetes-bootcamp
```

### [Scaling](https://kubernetes.io/docs/tutorials/kubernetes-basics/scale/scale-intro/)

```bash
kubectl expose deployment/kubernetes-bootcamp --type="LoadBalancer" --port 8080
kubectl get rs # get replica sets
kubectl scale deployments/kubernetes-bootcamp --replicas=4 # set replicas
kubectl get pods -o wide

kubectl describe deployments/kubernetes-bootcamp #

# normal
export NODE_PORT="$(kubectl get services/kubernetes-bootcamp -o go-template='{{(index .spec.ports 0).nodePort}}')"
curl http://"$(minikube ip):$NODE_PORT"
# docker desktop (Mac)
minikube service kubernetes-bootcamp --url
curl 127.0.0.1:<port>

kubectl scale deployments/kubernetes-bootcamp --replicas=2 # this will take some time, until they are terminated

```

# ---

https://kubernetes.io/docs/tutorials/kubernetes-basics/expose/
