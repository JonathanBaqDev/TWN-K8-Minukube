## Namespaces

Defining namespaces can provide better organization and isolation of resources in a cluster, which is useful when working with a complex cluster that has multiple applications and teams.
- Check existing namespaces with `kubectl get namespace`
- ConfigMaps and Secrets are namespace-scoped, so create them in the namespace where their workloads need them.
- Services can be reached across namespaces using their fully qualified DNS name, such as `service-name.namespace.svc.cluster.local`.
- Set a resource's namespace in its configuration file, or apply it with `kubectl apply -f config-file.yaml --namespace=my-namespace`.
- A tool like [kubectx](https://github.com/ahmetb/kubectx) can make it easier to work with namespaces.