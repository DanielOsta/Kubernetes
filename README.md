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

kubectl proxy # Will create a proxy that will forward communications into the private network (my terminial)

# Get the pod name
export POD_NAME=$(kubectl get pods -o go-template --template '{{range .items}}{{.metadata.name}}{{"\n"}}{{end}}')
echo Name of the Pod: $POD_NAME

# Access the Pod through the proxied API
curl http://localhost:8001/api/v1/namespaces/default/pods/$POD_NAME/
```


`kubectl` commands: https://kubernetes.io/docs/reference/kubectl/

Up next: `https://kubernetes.io/docs/tutorials/kubernetes-basics/explore/explore-intro/`