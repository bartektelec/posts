# Setting up Home assistant with Zigbee integrations using Kubernetes

## Hardware
- Raspberry PI 5 4gb
- Conbee 2 USB-stick

## Software
- Ubuntu 24.04 lts
- Home assistant 24.11

Installed k3s

Every action performed with kubectl needed to be done with su permissions.

All kubernetes components are created with `--dry-run=client -o yaml > file_name.yaml` so they can easily be edited and reapplied.

## Create a kube deployment with homeassistant image
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

## Zigbee2MQTT

There are several ways to control zigbee devices with HA, I previously installed deCONZ, but it needed me to discover new devices using a separate Web UI that I could only use directly from the device and wasn't exposed to rest of my local network - probably something I was doing wrong IDK. It also imported my devices but they never updated, that issue is mentioned in the docs and has somehting to do with how WebSockets are setup with the deCONZ app.
I later figured Zigbee2MQTT supports way more devices, in case I want to add some less known devices in the future. 

There is a way to install Zigbee2MQTT using HA addons, but HA running on a container doesn't support addons.

Zigbee2mqtt is a bridge that allows Zigbee data to be passet to an MQTT broker.
Zigbee2MQTT -> Mosquitto MQTT Broker -> MQTT Integration in HA

Using the official zigbee2mqtt tutorial: https://www.zigbee2mqtt.io/guide/installation/02_docker.html
I use the same image and try to "translate" the docker run command to a kubernetes component.
```
kubectl create deploy zigbee2mqtt-deploy --image=docker.io/koenkk/zigbee2mqtt:latest --port=8080 --dry-run=client -o yaml > zig2mqtt-deploy.yaml
```

We need to add environment variables for the container in `spec.template.spec.containers`
```
          env:
            - name: TZ
              value: Europe/Oslo
            - name: "ZIGBEE2MQTT_DATA"
              value: "/data"
            - name: ZIGBEE2MQTT_CONFIG_MQTT_SERVER
              value: "mqtt://mosquitto"
            - name: ZIGBEE2MQTT_CONFIG_HOMEASSISTANT
              value: "true"
```

to access hardware devices it will also need a
```
securityContext:
  privileged: true
```

and also it will need persistant volumes so the settings won't get lost after restart
```
volumeMounts:
  - name: zigbee2mqtt-data
    mountPath: /app/data
  - name: zigbee2mqtt-udev
    mountPath: /run/udefv
  - name: ttyacm
    mountPath: /dev/ttyACM0

```

```
volumes:
  - name: zigbee2mqtt-data
    persistentVolumeClaim:
      claimName: zigbee2mqtt-data
  - name: zigbee2mqtt-udev
    hostPath:
      path: /run/udev
  - name: ttyacm
    hostPath:
      path: /dev/ttyACM0
```

persistant volumes need a separate PVC yaml declaration:
```
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: z2m-data
  namespace: homeassistant
spec:
  accessModes:
    - ReadWriteOnce
  resources:
    requests:
      storage: 1Gi
```

then a service for zigbee2mqtt
```
kubectl expose deploy zigbee2mqtt-deploy --name zigbee2mqtt-svc --type=LoadBalancer --port=8080 --dry-run=client -o yaml > zigbee2mqtt-svc.yaml
```

## Mosquitto
Next up mosquitto MQTT broker needs to be set up https://moreillon.medium.com/encrypted-mosquitto-mqtt-broker-in-kubernetes-26bb7acd11c7

Deployment:
```
apiVersion: apps/v1
kind: Deployment
metadata:
  name: mosquitto
spec:
  replicas: 1
  selector:
    matchLabels:
      app: mosquitto
  template:
    metadata:
      labels:
        app: mosquitto
    spec:
      containers:
      - name: mosquitto
        image: eclipse-mosquitto
        ports:
        - containerPort: 8883
        - containerPort: 9001
        volumeMounts:
        - mountPath: /etc/mosquitto/
          name: config
        - mountPath: /mosquitto/certs/
          name: certs
        - mountPath: /mosquitto/data/
          name: data
      volumes:
      - name: config
        configMap:
          name: mosquitto-config
      - name: certs
        secret:
          secretName: mosquitto-certs
      - name: data
        persistentVolumeClaim:
          claimName: mosquitto-data

```
ConfigMap:
```
apiVersion: v1
kind: ConfigMap
metadata:
  name: mosquitto-config
data:
  mosquitto.conf: |

    persistence true
    persistence_location /mosquitto/data/
    log_dest stdout
    
    # MQTTS listener
    listener 8883
    protocol mqtt

    cafile /etc/ssl/certs/ca-certificates.crt
    keyfile /mosquitto/certs/tls.key
    certfile /mosquitto/certs/tls.crt

    # WS Listener
    listener 9001
    protocol websockets

```

PVC:
```
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: mosquitto-data
spec:
  accessModes:
    - ReadWriteOnce
  resources:
    requests:
      storage: 5Gi

```
Service:
```
apiVersion: v1
kind: Service
metadata:
  name: mosquitto-mqtts
spec:
  type: NodePort
  selector:
    app: mosquitto
  ports:
  - port: 8883
    nodePort: 30883
---
apiVersion: v1
kind: Service
metadata:
  name: mosquitto-ws
spec:
  type: ClusterIP
  selector:
    app: mosquitto
  ports:
  - port: 9001
```
