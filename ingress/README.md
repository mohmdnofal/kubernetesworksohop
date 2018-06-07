# Introduction
This toturial was inspired by the [Nginx Ingress Demo](https://github.com/nginxinc/kubernetes-ingress/tree/master/examples/complete-example).

In this excersise we depoly NGINX Contoller, expose it to the public using Azure Load Balancer. after this we deploy a simple web application and then configure load balancing for that application using the Ingress resource.

## 1. Deploy the Ingress Controller
Deploy the default server secret
```
kubectl apply -f default-server-secret.yaml
```
Deploy the config map, ours is empty
```
kubectl apply -f nginx-config.yaml
```
Deploy the ingress controller, 2 options are availbale as a deployment or as a DaemonSet
```
kubectl apply -f nginx-ingress.yaml
or
kubectl apply -f nginx-ingress-dd.yaml
```
Create a service to assign the contoller a public IP address on Azure Load Balancer
```
kubectl apply -f loadbalancer.yaml
```
record your public IP address, getting the External IP could take ~5 minutes
```
kubectl get svc nginx-ingress
ingreeEIP=W.X.Y.Z
```
## 2. Deploy the Cafe Application
Create the coffee and the tea deployments and services:
```
kubectl apply -f cafe.yaml
```
## 3. Configure Load Balancing
Create a secret with an SSL certificate and a key:
```
kubectl apply -f cafe-secret.yaml
```
Create an Ingress resource:
```
kubectl apply -f cafe-ingress.yaml
```
## 4. Test the Application
If you are using Azure DNS, you need to configure an A record in your example.com domain
```
az network dns record-set a add-record -g MyResourceGroup -z example.com -n cafe -a $ingreeEIP
```
Open your browser and test cafe.example.com/coffee and /tea or use curl
```
curl http://cafe.example.com/coffee
curl http://cafe.example.com/tea
curl http://cafe.example.com   ##you should get 404 on this one
```