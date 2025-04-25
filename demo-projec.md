# Demo project

> src: <https://www.youtube.com/watch?v=s_o8dwzRlu4>

## Overview

- Deploy MongoDB
- Deploy web application
- Connect web application to MongoDB using service
- Config web application and MongoDB using Secret and Config map
- Create external service for external access

## Prerequisite

- docker
- minikube
  minimum requirement
  - 2 CPUs or more
  - 2GB of free memory
  - 20GB of free disk space
  - Internet connection

## Setup minikube

- follow minicube installation step from <https://minikube.sigs.k8s.io/docs/start/?arch=%2Fwindows%2Fx86-64%2Fstable%2F.exe+download>

- start up minicube using docker as driver

``` bash
minicube start --driver docker
```

- check status mincube

```bash
minikube status
```

- checking node in cluster

```bash
kubectl get node
```

## Create config map & secret for MongoDB

- create config map file "mongo-config.yaml" follow guide from kubernetes website

- in metadata section set name as "mongo-config"

example

```yaml
metadata:
  name: mongo-config
```

- in data section set key "mongo-url" value as "mongo-service"

- create secret file "mongo-secret.yaml" follow guide from kubernetes website

- in metadata section set name as "mongo-secret"

- in data section set key "mongo-user" value "mongouser" in **base64 encryption only value** and key "mongo-password" value "mongopassword" in **base64 encryption only value**

## Create MongoDB deployment & service

- create mongo.yaml deployment follow guide from kubernetes website

> this mongo.yaml will contain both deployment and service this is common practice since every deployment will need service

- in meatadata section in labels section set key "app" value "mongo" this will used by service to reference

``` yaml
  labels:
    app: mongo
```

- in spec section set key "replica" value 1 since we need only one MongoDB pod

- then create mongo template under spec section

``` yaml
  template:
    metadata:
      labels:
        app: mongo
    spec:
      containers:
      - name: mongodb
        image: mongo:5.0
        ports:
        - containerPort: 27017
        env:
          - name: MONGO_INITDB_ROOT_USERNAME
            valueFrom: 
              secretKeyRef:
                name: mongo-secret
                key: mongo-user
          - name: MONGO_INITDB_ROOT_PASSWORD
            valueFrom: 
              secretKeyRef:
                name: mongo-secret
                key: mongo-password
```

> get image from docker hub, this mongo image port start at 27017

- in spec section set selector to select template

``` yaml
  selector:
    matchLabels:
      app: mongo
```

- create mongo.yaml follow service guide from kubernetes website also put "---" to seperate configuration

- in spec section set selector to "mongo" to select deployment and forward port from 27017 to port 8080

```yaml
  selector:
    app: mongo
  ports:
    - protocol: TCP
      port: 8080
      targetPort: 27017
```

## Create webapp deployment & service

- create webapp.yaml and create deployment and service for image "nanajanashia/k8s-demo-app:v1.0" forward port to port 3000 set nodePort "30100"

> node port is use to expose service on node's ip at a static port must be in range 30000-32757

## Run

- listing pods

``` bash
kubectl get pod
```

- apply config & service file(-f)

``` bash
kubectl apply -f mongo-config.yaml
kubectl apply -f mongo-secret.yaml
```

- apply mongo and webapp file

```bash
kubectl apply -f mongo.yaml
kubectl apply -f webapp.yaml
```

- listing resouces

```bash
kubectl get all
kubectl get configmap
kubectl get secret
```

- If you can't access the NodePort service webapp with MinikubeIP:NodePort, execute the following command:

``` bash
minikube service webapp-service
```

> My result is still failure due to fetch not get response. This make css of container value is still "none" causing display white background. I dont think it was entire server.js failure but at "/get-profile" API path.
