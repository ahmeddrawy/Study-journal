# kubernetes crash course

Code: Yes
Created: Mar 21, 2021 3:52 PM
Importance: very important
Materials: https://www.youtube.com/watch?v=X48VuDVv0do
Reviewed: No
context: DevOps, kubernetes
source: YT

- table of content

# intro

orchestration tool for containers

# cons

- high availablity or no down time

- scalability

- disaster recovery

# k8s components

## pod

- smallest unit of k8s, is an abstraction over container
- usually 1 app per pod
- each pod has its own ip
- new ip address on re-creation

## service

- permanent ip address can be attached to each pod
- lifecycle of pod and service are not connected
- if pod dies service and ip stay

### external service

internal service

## ingress

forward requests to services

## configmap:

- external conf of your app
- dont put credentials ⚠️

## secret:

- like configmap but used for secrets
- used to store secret data
- encoded in base64
- ⚠️ ‼️ built-in securtiy mechanism is not enabled by default
- used data from `secret:` or `configmap:` as env variables or properties file

## volumes

we can attach either local or remote (outside k8s cluster)

k8s doesn't manage data persistence you have to manage it explicitly using volumes

## replica

- connected to the same service, another node but same service

- service is also load balancer

## Deployment:

- you wont create replica for pod but you will define blueprint for the pod which contains how many replica

stateless

## statefulSet:

we need DB replica too 
but we can't do this using Deployment becuase it has a state (its data)

- for statefull apps
    - mysql
    - elastic search
    - mongo DB
    - etc.
- deploying `statefulSet` not easy

DB are often hosted outside of K8S cluster

# K8S Architecture

## worker machine

- node - or server-

- each node has multiple pods

- worker do the actual work

## 3 processes must be installed on each node

### kubelet

- process of k8s itself

- interact with node and pods

- take the config and run the pod inside and assign resources for pods

### container runtime

- docker for example

### kubeproxy

- foward the requests

- has intellegient forward logic to make it good performance

# how to interact with cluster

## master node

master processes 

### 1 - api server

- cluster gateway

- acts as a gateway for authentication

- only 1 entrypoint to the cluster
- **slide**

    ![kubernetes%20crash%20course%20fd17989ab85d4536aa18bb784813440f/Untitled.png](kubernetes%20crash%20course%20fd17989ab85d4536aa18bb784813440f/Untitled.png)

### 2 - scheduler

- **slide**

    ![kubernetes%20crash%20course%20fd17989ab85d4536aa18bb784813440f/Untitled%201.png](kubernetes%20crash%20course%20fd17989ab85d4536aa18bb784813440f/Untitled%201.png)

### 3 - controller manager

### 4 - etcd(cluster brain)

application data not stored in etcd 

- **slide**

    ![kubernetes%20crash%20course%20fd17989ab85d4536aa18bb784813440f/Untitled%202.png](kubernetes%20crash%20course%20fd17989ab85d4536aa18bb784813440f/Untitled%202.png)

## we can multiple master nodes

- which have its master processes

- api server is load balanced

- etcd is distributed storage accross all master nodes
- **slide**

    ![kubernetes%20crash%20course%20fd17989ab85d4536aa18bb784813440f/Untitled%203.png](kubernetes%20crash%20course%20fd17989ab85d4536aa18bb784813440f/Untitled%203.png)

## example cluster

- master nodes resources is important but it need less resources bec it has less work load