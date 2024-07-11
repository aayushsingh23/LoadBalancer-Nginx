
# LoadBalancing using Nginx

It generate 2 backend pods ( Deployments ) such that they act as LoadBalancers.

It works in way such that the service routes requests to the deployment with least number of active requests.

## How least_conn Load Balancing Works
The least_conn directive tells NGINX to forward each new request to the server with the fewest active connections. This helps distribute the load more evenly among the backend servers, especially when some requests take longer to process than others.

**Detailed Load Balancing Flow**

Incoming Request to NGINX: When a request comes to the NGINX service, NGINX checks the configuration to determine how to handle the request.

*Selecting a Backend Server:*

* *Least Connections:* NGINX looks at the number of active connections each backend server currently has.

* *Choosing the Server:* It selects the backend server with the least number of active connections to handle the new request.

*Forwarding the Request:* The request is then forwarded to the selected backend server. This happens transparently to the client making the request.

*Processing the Request:* The backend server processes the request and sends the response back to NGINX.

*Returning the Response:* NGINX then forwards the response back to the client.

## Deploying Backend-Pods and Services

Generating deployments for backend pods

```bash
    kubectl apply -f backend1.yaml
    kubectl apply -f backend2.yaml
```

Generating services for the deployments

```bash
    kubectl apply -f backend1-service.yaml
    kubectl apply -f backend2-service.yaml
```
## Deploying LoadBalancing Deployements and Services

Generating deployment

```bash
    kubectl apply -f deploy-loadbalance.yaml
```
Generating services for the deployment
```bash
    kubectl apply -f deploy-service.yaml
```

## Testing Your Backend Services

Scale your deployements to see Load Balancing in action

```bash
    kubectl scale deployment backend1-deployment --replicas=3
    kubectl scale deployment backend2-deployment --replicas=3
```
Start Minikube Tunnel
```bash
    minikube tunnel
```
Access your NGINX service. Get the external IP of you service

```bash
    kubectl get svc nginx-loadbalance-service
```
It should be something like `127.0.0.1`.

Send requests to your server
```bash
    for ($i=1; $i -le 10; $i++) {
        Invoke-WebRequest -Uri http://127.0.0.1 -UseBasicParsing
    }
```
Check the logs of your pods so see Load Balancing in action.

```bash
    kubectl get pods -l app=backend1
    kubectl get pods -l app=backend2
```

Use the following command to check logs, copy the name of the pod,
say the name is `backend1-deployment-aayushsingh`

```bash
    kubectl logs backend1-deployment-aayushsingh
```

## Documentation

[Kubernetes](https://kubernetes.io/docs/home/)

[Minikube](https://minikube.sigs.k8s.io/docs/)

[Nginx](https://nginx.org/en/docs/)

