# Kubernetes Crash Course for Absolute Beginners

> main src : <https://www.youtube.com/watch?v=s_o8dwzRlu4>

## what is k8s

k8s is orchestration tool used to help manage containerized application

## The needs

Dues to rise of microservices increase number of containers across mutilple enviroment. It become hard to manage.

## What k8s offer

- High Availability(HA)
- scalability
- Disaster recovery(DR)

## k8s architecture

1. **master node**
    manage cluster
    - api server: entry points to k8s
    - controller manager: keep track of cluster
    - scheduler: ensure pod placement
    - etcd: store cluster state and backup cluster state

2. **worker node**
    running application
    - pod: smallest unit, abstract over container, 1 app/pod, have own dynamic ip(get new ip on recreation)
    - service: have own static ip used for entry points of pods(by deployment)
    - ingress: entry point of service to external, add TLS for HTTPS, expose service via same ip/domain
    - config map: keep config of application(insecure)
    - secret: store secret data in base64(still insecure need 3rd party k8s tool)
    - volume: store data that need persistent(can be local or remote)eg. db
    - deployment: blueprint of pods, used to generate stateless application replica
    - StatefulSet: like deployment but for stateful application like db

3. **kubelet**
    running inside master and worker node, enable communication between node and excuting command in node

> deployment can use to create pod for stateful application if only **1 replica**

## k8s configuration

to config k8s component you need to comunicate with api server of k8s. there are 3 ways to communicate with api server of k8s

1. UI
2. API
3. CLI(kubectl)

we using these channel to send configuration such as deployment then k8s will check if actual state is same as desire state(config)

then k8s will try to make actual state match desire state

## Configuration file

each configuration file has 3 parts

1. metadata: name
2. spec: specification of component
3. status: actual state automated by k8s

> you can lookup config file in <https://kubernetes.io/docs/concepts/>

## why minikube

normally to in production k8s will have multiple master and worker node across multiple virtual or physical machine

but if you want to test deployment on local machine It is difficult or impossible

this is when minikube come in **minkube can run master process and worker process run in one node**
