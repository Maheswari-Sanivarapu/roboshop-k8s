for manifest.yaml
1. In this mysql we are creating the pod which is smallest deployment unit and inside the pod the container is created, the container consists of base image of our application we are taking the image-name here as redis:7.0 from docker hub and adding the image-name inside container 
2. creating the replicas here like if we keep replicas as 1 only one pod will run and if we delete the pod the replica will create the new pod again so here replica here is used to match the pods mentioned in replicas. to create replicas pod labels should match with replicas than only replicas will be created
3. creating the deployment here and attaching the replicas and pods to it so here deployment is used bcoz it will be updated with the latest images if there is any change in the image using pods the image will not be updated automatically,replicaset will use the old image for the pod
4. in order to make pod to pod communication we are creating the service and adding the port of mysql inside service and databases should not be exposed to outside world so we are not giving any type of service name like loadbalancer,NodePort
5. here we need password for mysql so we are creating the secrets using secrets we can encrypt the password of mysql and passing the values using secretRef inside the container 
6. keeping the resource in roboshop namespace here


for secret-manifest.yaml
1.create OIDC Provider

REGION_CODE=us-east-1 
CLUSTER_NAME=roboshop
ACC_ID=557690617909

eksctl utils associate-iam-oidc-provider \
    --region $REGION_CODE \
    --cluster $CLUSTER_NAME \
    --approve

2. create secret manager in AWS using aws secret manager and select the service as secretsmanager attach the ARN of secret manager where our password is stored and attach the ARN by creating the new IAM policy and add it in specific ARN and in text

3. Attach the IAM policy created in step 2 to the newly creating service account

eksctl create iamserviceaccount \
--cluster=$CLUSTER_NAME \
--namespace=roboshop \
--name=roboshop-mysql-password \
--attach-policy-arn=arn:aws:iam::$ACC_ID:policy/secretpasswordfetch \
--override-existing-serviceaccounts \
--region $REGION_CODE \
--approve

4. create the role and rolebinding to the service account
5. attach the service-account to the pod

6. create the volumes inside the pod, bcoz volumes inside the pod are ephermal means if pod is deleted then volume attached to the pod will also be deleted so creating the volume with emptydir means using this emptydir it can attach the volume to multiple containers within the pod like to init containers and also container which is main container

7. mounting the created volume inside the pod to init containers to use it and keeping one folder as /tmp to mount that folder

8. attaching the mounted volume to the main container i.e. container inside the pod

using this way init container will fetch the secret from the service account and give that secret to main container

service-account ---> init containers ---> container

fetch secrets
==============
follow above steps

1. Make sure secrets exists and SA have access through role and policy
2. Create initcontainer that fetch the secret and write to the volume. init container should have volume mounts to persist.
3. emptyDir is required to share the storage between containers in pod.
4. mount the same volume to the main container and access it..
5. before running command in container we need to read the secret and make it as env variable
6. now we can delete the file and start the container