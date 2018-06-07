# Configure your toolbox
We will build a docker image which will contain some of the necessary packages such as openssh-client, curl, and dnsutils... to troubleshoot issus in your cluster.

## Overview
In this guide we will:
* build a docker image
* tag it and push it to our registry (we will be using [Azure Container Registry](https://docs.microsoft.com/en-us/azure/container-registry/))
* run the image in kuberentes 
* copy our ssh key to the pod and change its permissions 

## Steps
Build the docker image 
```
docker build -t toolbox:latest .
```
Check the image
```
docker image ls toolbox
```
Login to your container registry
```
registryName=acreurope
az acr login --name $registryName
```
Get the login server to tag the images
```
loginServer=`az acr show --name $registryName --query 'loginServer'`
```
Tag the image and push it to your registry
```
docker tag toolbox $loginServer/toolbox/toolbox:0.0.1
docker push $loginServer/toolbox/toolbox:0.0.1
```
Check your image in the container registry
```
az acr repository list --name $registryName -o table
az acr repository show-tags --name registryName --repository toolbox/toolbox
```

Run your image in your Kubernetes cluster
```
kubectl run toolbox --image=$loginServer/toolbox/toolbox:0.0.1
or
kubectl apply -f toolbox.yaml
``` 

If you want to be able to SSH to your AKS nodes, copy your ssh keys to the running pod (if you chose to run the deployment from the YAML file, then make sure you delete the pod afterwoards), and from there you can SSH to your nodes
```
kubectl get pods
kubectl cp ~/.ssh/id_rsa toolbox-XXXXXXXXXX-XXXXX:/id_rsa
kubectl exec -it toolbox-XXXXXXXXXX-XXXXX -- ls -a id_rsa
kubectl exec -it toolbox-XXXXXXXXXX-XXXXX /bin/bash
ssh -i id_rsa <youradminuser>@<nodePrivateIP>
```



