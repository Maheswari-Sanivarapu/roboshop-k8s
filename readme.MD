Deployment---> replicas ---> pods ---> containers
container is a subset of pod
pod is a subset of replica
replica is a subset of deployment

first create the pods with labels attached
second create the replicas by taking the pod labels under metadata then only replicas will be created for the pod bcoz in order to create multiple pods the pod labels should be attached to replicas based on the labels replicas will crete the multiple pods
if any pod is deleted by using replicas it will create the pod immediately here replicas will match the number of replicas mentioned in the pod based on that it will create that many pods
third deployment will be created based on replicas

--> here deployment is nothing but deleting the old image and creating the new image by keeping image name as nginx:latest then if we apply then it will point to the latest nginx version and from the old image it will be unassigned and it will show as 0
kubectl describe deployment <deployment-name>

--> here by using replicas we cant delete the old image and create the new image and if we try to apply this then they won't be any changes to the pods
check using kubectl describe rs <replica-name>

kubectl get pods -o wide --> it show the pod ip address along with node-ip to which it is assigned
kubectl exec -it <pod-name> -- bash --> to login inside the container of the pod

## project:
1. In this project we are creating the pod which is smallest deployment unit and inside the pod the container is created, the container consists of base image of our application we are taking that image-name from docker hub and adding the image-name inside container
2. in order to make pod to pod communication we are creating the service and also to expose our application to outside world like in the browser who can access we are creating the service 