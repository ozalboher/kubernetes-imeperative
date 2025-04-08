# Creating objects in kubernetes in imperative way:
## The pod object:
- The pod object is the smallest deployable unit in Kubernetes. It can be used to run a single container or a group of containers that need to share resources.
- Pods can share resources like volumes and network.
- Pods have an internal cluster IP address and can communicate with each other using this address.(localhost address) - similar to the concept of an ecs task.
## Controller object:
- The controller object is a higher-level abstraction that manages the lifecycle of pods. It ensures that the desired state of the application is maintained by creating, updating, and deleting pods as needed.
- Controllers can be used to manage the scaling, rolling updates, and self-healing of applications.
- Each deployment object will set a blueprint for the pod object.So for different pod objects, we will have different deployment objects.

# Getting started with a basic deployment to a kubernetes cluster:
## Prerequisites:
* Docker installed
* Minikube installed
* Kubectl installed

- first we obviously need to build the image:
```bash
docker build -t kub-first-app .
```
- check with minikube if the cluster is running:
```bash
minikube status
```
- if not running, start the cluster:
```bash
minikube start --driver=docker     
```
- Create a deployment object with kubectl(it should connect automatically to the minikube cluster):
* First Push the image to Docker hub first (the minikube cluster will not be able to access the local docker image):
```bash
docker tag kube-first-app ozalboher/kube-first-app
docker push ozalboher/kube-first-app
```
- Create the deployment object:
```bash
kubectl create deployment kube-first-app --image=ozalboher/kube-first-app
```
- Check the deployment object:
```bash
kubectl get deployment
```
- Check the pod object:
```bash
kubectl get pods
```
* To see the cluster with the pod we just created:
```bash
minikube dashboard
```
* On the browser, you can now see the control panel of the kubernetes cluster.
* The control panel is essentially acting as the Master node of the cluster. Which is suppose to analyze the running pod and find the best working node for it. A worker node as we learned is containing the pod and the container and kubelet is in charge to manage the worker node (the pod and the container).