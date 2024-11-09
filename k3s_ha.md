# Setting up Home assistant with Zigbee integrations using Kubernetes

Installed k3s

All kubernetes components are created with `--dry-run=client -o yaml > file_name.yaml` so they can easily be edited and reapplied.

- Create a kube deployment with homeassistant image
```
kubectl create deploy home-assistant-deploy --image=docker.io/homeassistant/home-assistant:stable --http-port=8123 --dry-run=client -o yaml > ha-deploy.yaml
```
this command creates a deployment yaml file for kubernetes with specified `containerPort` to 8123 and uses the official homeassistant image

- Next need to expose it, generate a LoadBalancer service that exposes port 8123 through TCP
```
kubectl create service loadbalancer home-assistant-service --tcp=8123:8123 --dry-run=client -o yaml > ha-svc.yaml
```

Now simply applying both deployment and service won't work, what is needed is to change the `spec.selector.app` value in the `ha-svc.yaml` so it has the same value as our deployment, so `home-assistant-deploy`

After running 
```
kubectl apply -f ha-deploy.yaml
kubectl apply -f ha-svc.yaml
```
we should have the 8123 port exposed and can fire up the browser and proceed to `http://{cluster_ip_addr}:8123` where we will find a getting started wizard for home-assistant
