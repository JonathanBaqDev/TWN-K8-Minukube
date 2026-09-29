## Gateway API vs Ingress

Ingress provides a straightforward way to route external HTTP(S) traffic by host or path, but it tightly couples application routing and TLS configuration in a single resource, linking application changes to shared entry-point settings.

For more complex clusters, Gateway API presents a modular, role-oriented alternative that separates shared entry-point configuration from application routing:

- **GatewayClass** identifies the controller implementation, such as NGINX, Istio, or Cilium, and the capabilities it provides.
- **Gateway** defines listeners, such as ports, hostnames, and TLS configuration. Depending on the controller and environment, creating a Gateway may also provision infrastructure such as a cloud load balancer.
- **HTTPRoute** defines application host and path rules and references a Gateway through `parentRefs`. This lets application teams manage routes without changing shared listener or TLS configuration, subject to the Gateway's attachment policy.

This separation helps platform teams manage shared infrastructure while application teams manage their own routes. 

Multiple routes and services can share a Gateway; HTTPRoute backend weights can split traffic, for example 80/20 for a canary release, without relying on controller-specific annotations. 

Gateway API also defines route types for other protocols, such as `TCPRoute` and `UDPRoute`, but availability depends on the controller and installed API support.
