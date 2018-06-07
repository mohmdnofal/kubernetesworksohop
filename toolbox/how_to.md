#build the contianer 
docker build -t toolbox:0.0.1 .
#check the built image
docker image ls

#login to your repository 
az acr login --name acreurope 

#tag the image to be sent to a repository
docker tag toolbox:0.0.3 acreurope.azurecr.io/toolbox/toolbox:0.0.3

#push the image to the registry 
docker push acreurope.azurecr.io/toolbox/toolbox:0.0.3

#check the image in acr
az acr repository list --name acreurope -o table
az acr repository show-tags --name acreurope --repository toolbox/toolbox

#Build your k8s file
kubectl create -f toolbox.yaml

#copy your ssh file to the contianer
kubectl cp ~/.ssh/id_rsa toolbox-784c4bb695-mnqdl:/id_rsa
kubectl exec -it toolbox-784c4bb695-mnqdl -- ls -a id_rsa



