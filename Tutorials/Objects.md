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
* Minikube installed - minikube is a tool that creates a local Kubernetes cluster on your machine. It is a great way to get started with Kubernetes and test your applications locally.
* Kubectl installed - kubectl is the command-line tool for interacting with Kubernetes clusters. It allows you to create, update, and delete resources in your cluster. 

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
## Create a deployment object with kubectl(it should connect automatically to the minikube cluster):
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
* The control panel is essentially acting as the Master node of the cluster. Which is suppose to analyze the running pod and find the best working node for it. A worker node as we learned is containing the pod and the container and kubelet is in charge to manage the worker node (the pod and the container). kubelet is the agent that runs on each worker node and communicates with the master node to ensure that the desired state of the application is maintained.(it is done automatically by the kubernetes cluster).

# Exposing a pod created by a deployment object:
- To expose the pod created by the deployment object, we can use the kubectl expose command. This command creates a service object that exposes the pod to the outside world.
```bash
kubectl expose deployment first-app --type=LoadBalancer --port=8080
```
- Check the service object:
```bash
kubectl get service
```
- The external IP address of the service will be displayed in the output. This is the address that can be used to access the application running in the pod.
- When you see <pending> in the external IP address, it does not mean it is still being created like we would think, because the minikube cluster is not a real cluster (it is a VM) and does not have a load balancer. Instead, it means that the service is not accessible from outside the cluster. To access the service, we can use the minikube service command.
- To view the app running on the cluster, we can use the minikube service command. This command opens a browser window with the URL of the service.
```bash
minikube service first-app
```
- The Service object is a stable endpoint that can be used to access the application running in the pod. It provides a way to access the application without having to know the IP address of the pod. The Service object also provides load balancing and service discovery for the application.
- You can use the scale command to scale the deployment object. This command changes the number of replicas of the deployment object. The number of replicas is the number of pods that will be created by the deployment object.
```bash
kubectl scale deployment first-app --replicas=3
```
* This is useful for scaling the application up or down based on the load(if there is no autoscaling or loadbalancer). The deployment object will automatically create or delete pods to match the desired number of replicas.

- To update the deployment object, we can use the kubectl set image command. This command updates the image of the deployment object. The new image will be used to create new pods.
* Make sure to locally build the new image(and give it a new tag - if not the kubectl will not consider it as new, and wont update the pod) and push it to docker hub before running this command.
```bash
kubectl set image deployment/first-app ozalboher/kube-first-app:latest
```
* You can also use the rollout command to see the status of progress of the deployment object. This command shows the status of the deployment object and the pods that are created by it.
```bash
kubectl rollout status deployment/first-app
```
* incase of a typo in the name of the image or an unsuccessful build, you can use the kubectl rollout undo command to rollback the deployment object to the previous version. This command will create new pods with the previous image.
```bash
kubectl rollout undo deployment/first-app
```
* You can also rollout to a prior version by specifying the revision number. The revision number is the number of the version that you want to rollback to.
```bash
kubectl rollout undo deployment/first-app --to-revision=1
```
## Cleaning things up:
- To delete the deployment object, we can use the kubectl delete command. This command deletes the deployment object and all the pods that are created by it.
```bash
kubectl delete deployment first-app
```
- To delete the service object, we can use the kubectl delete command. This command deletes the service object and all the pods that are created by it.
```bash
kubectl delete service first-app
```