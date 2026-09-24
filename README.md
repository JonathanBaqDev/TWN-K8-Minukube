# Kubernetes Minikube 
Reference project: https://gitlab.com/twn-devops-bootcamp/latest/10-kubernetes/demo-deploying-application/-/tree/starting-code?ref_type=heads

Configuration files to launch a Kubernetes cluster for Mongo Express and Mongo DB.

## Mongo DB
- DB root user credentials on the secrets configuration need to be encoded, eg. base64 - remember to use strong passwords
- Create the secret before the deployment

```bash
minikube start
kubectl apply -f mongo-secret.yaml
kubectl apply -f mongo-db.yaml

kubectl get all
kubectl get all | grep mongodb

kubectl get service
kubectl describe service "service-name"

kubectl get pod -o wide # To see pod IPs
kubectl describe "pod-name"
```

## Mongo Express
- Create config map before referencing in the mongo-express deployment

```bash
kubectl apply -f mongo-config.yaml
kubectl apply -f mongo-express.yaml

kubectl logs "mongo-express-deployment-name"

# To access on the browser
kubectl get service
minikube service mongo-express-service 
```
## Namespaces

- Defining namespaces can provide better organisation and isolation of resources in a cluster which is useful when working with a compelx cluster with multiple applications and teams 
- Check existing namespaces with `kubectl get namespace`
- Resources can't generally access other resources in different namespaces (eg. ConfigMaps and Secrets need to be created separately for each namespace)
- Services (eg. DB service) can be accessed accross different namespaces
- You can set the namespace for resources when running `kubectl apply "congig-file" --namespace=my-namespace` or defining in the configuration files themselves - this provides better visibility and control as it will be checked in to source control.
- Using a tool like [kubectx](https://github.com/ahmetb/kubectx) can make it easier to manage working with namespaces