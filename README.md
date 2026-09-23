# Kubernetes Minikube 
Reference project: https://gitlab.com/twn-devops-bootcamp/latest/10-kubernetes/demo-deploying-application/-/tree/starting-code?ref_type=heads

Configuration files to launch a Kubernetes cluster for Mongo Express and Mongo DB.

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