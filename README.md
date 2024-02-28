# Kubernetes
Going through the Kubernetes tutorial on [kubernetes.io](kubernetes.io)

```bash
minikube start
minikube dashboard # Opens the Kubernetes dashboard
kubectl create ... # Create deployment
kubectl get deployments # View deployments
kubectl create deployment hello-node --image=registry.k8s.io/e2e-test-images/agnhost:2.39 -- /agnhost netexec --http-port=8080 # create test pod
kubectl get pods # view the pods
kubectl get events # view the cluster events
kubectl config view # display the configuration
kubectl expose deployment hello-node --type=LoadBalancer --port=8080 # expose pod as a service
kubectl get services # view services
minikube service hello-node # only for minikube - to view the service
```

### Clean up
```bash
kubectl delete service hello-node
kubectl delete deployment hello-node
minikube stop
# Optional
minikube delete
```

### `kubectl` basics
```bash
# Basic: kubectl action resource
kubectl get nodes --help

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
```

Up next: `https://kubernetes.io/docs/tutorials/kubernetes-basics/expose/expose-intro/`
