# Ingress Actions in Kubernetes
- In this repository, I will be explaining what is ingress and why we need it and how we can acieve load balancing with the same.


## Agenda
- What is Ingress and why we need it? 🧐
- Types of Ingress 🧬
- Ingress Controllers, Rules and Resources 🙌 
- CRUD actions in Ingress!!! ✊
- Demo 💻
- Best Practices ✍️
- References 🧠


### What is Ingress?
- Ingress is an API object that manages external access to services inside cluster.
- Acts as a load balancer to handle the traffic for incoming requests to services.
- We need ingress to support centralized routing.
- Supports Layers-7 Routing
- Handles path-based routing
- Provides advanced routing techniques
- Handles TLS Termination


### Types of Ingress?
- Single Service: Allows to expose to single service while specifying default backend with no rules as such.
- Simple Fanout: It basically routes traffic from one IP address to multiple services, which can achieved by specifying host and URL paths in rules.
- Name based VH (Virtual Hosting): It basically routes traffic from one IP address to multiple hosts, which again can be specified under rules as host.
- SSL/TLS: We can secure our Ingress with secrets which contains TLS certificate and private keys. Note: Both host in TLS and rules section should match.
- Load Balancing: Gets controlled internally with ingress controller using load balancing algorithms like Round-Robin, Least Connection, IP-Hash, etc.


### Ingress Controller
- Ingress controllers responsible for implementing ingress rules to external load-balancer, reverse proxy, etc.
- Path-Based Routing: Routing based on URL paths
- Host-Based Routing: Routing based on hostname
- Load Balancing
- Handles SSL/TLS termination
- Types of popular ingress controllers: Nginx Ingress Controller, Traefik, HAPROXY


### Ingress Rules
- Purpose of Ingress rules is to route incoming traffic to services based on specific configuration.
- We can specify routing based on URL paths, hostnames, HTTP attributes, etc.
- We can also provide annotation to manifest file to support addon configuration to handle traffic.


### Ingress Resources
- Ingress resource is an instance of Ingress API object used to define and configure external access to services within the cluster
- Rules: Handles routing for incoming traffic
- Resources: Defined as K8s API which configure the rules 


### CRUD actions in Ingress
Operation | Command |
--------- | ------- |
Create    | ```kubectl create ingress simple --rule="foo.com/bar=svc1:8080,tls=my-cert"``` |
List      | ```kubectl get ingress ```
Describe  | ```kubectl describe ingress <ingress-name>```
Describe with Namespace | ```kubectl describe ingress <ingress-name> -n <namespace>```
Update    | ```kubectl edit ingress <ingress-name>```
Update with Namespace | ```kubectl edit ingress <ingress-name> -n <namespace>```
Delete | ```kubectl delete ingress <ingress-name>```
Delete with Namespace | ```kubectl delete ingress <ingress-name> -n <namespace>```


### Demo of basic ingress routing with different services
#### Create Namespace
- Create a namespace named shopping-mall
``` kubectl create namespace shopping-mall ```

#### To create deployments and pods and expose on port 8080
- Clothing deployment
``` kubectl create deployment clothing-app --image=hashicorp/http-echo -n shopping-mall ```
``` kubectl expose deployment clothing-app --type=NodePort --port=8080 -n shopping-mall ```

- Groceries deployment
``` kubectl create deployment groceries-app --image=hashicorp/http-echo -n shopping-mall ```
``` kubectl expose deployment groceries-app --type=NodePort --port=8080 -n shopping-mall ```

- Electronics deployment
``` kubectl create deployment electronics-app --image=hashicorp/http-echo -n shopping-mall ```
``` kubectl expose deployment groceries-app --type=NodePort --port=8080 -n shopping-mall ```

#### Create Ingress
- Create an ingress to route the traffic to the path with different host names
```  kubectl apply -f ingress.yaml -n shopping-mall ```

#### Note: We need to map the ingress ip in /etc/hosts via giving administrator privilegas
- You can check IP via this command ```kubectl describe ingress shopping-mall-ingress -n shopping-mall```, You will see one Address
- You can also do ```minikube ip ``` if you have minikube installed.

#### Check via pinging the hostnames
- ping clothing.example.com
- ping electronics.example.com
- ping groceries.example.com


### Demo of basic ingress routing with ssl/tls termination
- To create private key
```openssl genrsa -des3 -out domain.key 2048  ```

- To create certificate
`openssl req \
       -newkey rsa:2048 -nodes -keyout domain.key \
       -x509 -days 365 -out domain.crt`

- Create secret in K8s
```kubectl create secret tls tls-secret --cert=domain.crt --key=domain.key```

- Create the ingress for tls termination
```kubectl apply -f secret-ingress.yaml ```

- To check if SSL/TLS is terminated or decyrpted
```kubectl describe ingress secret-ingress ```


### Best Practices
- To optimise ingress performance for highly-scalable systems or high traffic routing, we can horizontally scaling IGC on traffic patterns.
- Implement Rate-limiting
- Optimise SSL/TLS Termination
- IGC may face limitations while handling high traffic routing whereas we can also implement traffic management configurations for the same.
- For security purposes, we could implement network policies for network flow, enable SSL/TLS for encrypted configuration, etc.
- Do monitor your ingress configuration using tools like Grafana. (That’s what we do)


### Refernces
- https://kubernetes.io/docs/concepts/services-networking/ingress/
- https://kubernetes.io/docs/concepts/services-networking/ingress-controllers/
- https://matthewpalmer.net/kubernetes-app-developer/articles/kubernetes-ingress-guide-nginx-example.html
- https://www.udemy.com/course/certified-kubernetes-application-developer
- Note: Here' the published link for the slide: https://docs.google.com/presentation/d/e/2PACX-1vTqqAJVakVOvop8iwGWx82O4LXxpSR5q80MuuShA9uxzsffezAK8GsvEWfXoHK1PlMaMMknWkkyDg_q/pub?start=false&loop=false&delayms=3000