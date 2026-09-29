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

Defining namespaces can provide better organization and isolation of resources in a cluster, which is useful when working with a complex cluster that has multiple applications and teams.
- Check existing namespaces with `kubectl get namespace`
- ConfigMaps and Secrets are namespace-scoped, so create them in the namespace where their workloads need them.
- Services can be reached across namespaces using their fully qualified DNS name, such as `service-name.namespace.svc.cluster.local`.
- Set a resource's namespace in its configuration file, or apply it with `kubectl apply -f config-file.yaml --namespace=my-namespace`.
- A tool like [kubectx](https://github.com/ahmetb/kubectx) can make it easier to work with namespaces.

## Ingress

- Ingress routes external HTTP(S) traffic to services using rules such as host names and paths. This avoids exposing each application directly through an external service.
- Traffic usually reaches the cluster through a cloud provider's load balancer or a proxy server. An Ingress controller receives and processes the requests.
- For this local Minikube setup, install an Ingress controller to handle this routing. Depending on your setup, `minikube tunnel` may be needed to provide external access.
- Installation instructions for the NGINX Ingress Controller are available [here](https://docs.nginx.com/nginx-ingress-controller/install/helm/open-source/).
- As an example, we will expose the Minikube Dashboard through an Ingress resource:
```bash
minikube dashboard
kubectl get ns
kubectl get all -n kubernetes-dashboard
```
- Create an Ingress rule to route requests for a custom host name to the Dashboard service. See [dashboard-ingress.yaml](dashboard-ingress.yaml):
```bash
kubectl apply -f dashboard-ingress.yaml
kubectl get ingress -n kubernetes-dashboard
```
- Add a mapping for `dashboard.example.com` to your Minikube IP in your local hosts file. If your setup requires it, run `minikube tunnel`.
- Open **dashboard.example.com** in your browser.

### Default backend for custom dashboard 404 response
- NGINX Ingress Controller can intercept HTTP error responses from an upstream service and send them to the Ingress default backend. This example replaces the Dashboard's 404 body with a custom message while preserving the 404 status.
- The custom response pod and Service are defined in [custom-redirect.yaml](custom-redirect.yaml). The Dashboard Ingress enables 404 interception and points its default backend at that Service in [dashboard-ingress.yaml](dashboard-ingress.yaml).
```bash
kubectl apply -f custom-redirect.yaml
kubectl apply -f dashboard-ingress.yaml

# Confirm the Ingress is accepted by the controller
kubectl describe ingress dashboard-ingress -n kubernetes-dashboard
```
- Open `http://dashboard.example.com/test` to request a Dashboard URL that returns 404. The response body should say **Dashboard page not found** and retain the 404 status.

### Configuring TLS
- Add the following to the ingress spec
```yaml
spec:
  tls:
    - hosts:
        - dashboard.example.com
      secretName: dashboard-tls-secret
```
- Replace the placeholders in [dashboard-tls-secret.yaml](dashboard-tls-secret.yaml) with the certificate and private key PEM contents before applying it. Do not commit a real private key to source control.
- Apply the Secret in the same namespace as the Dashboard, then apply the Ingress configuration.
- The `tls.crt` and `tls.key` values are the certificate and private key contents, not file paths.