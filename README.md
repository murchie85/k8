# K8 in a Nutshell 
    

![k8 image](https://39lxv6m650h1g391z2daj1l1-wpengine.netdna-ssl.com/assets/blog-kubernetes-og.jpg)  
    


# Navigation
  
1. [High Level Steps](#High-Level-Steps)
2. [Start up minikube](chapters/minikube/README.md)   
3. [Deployments](chapters/deployments/README.md)
4. [External Services & Ingress](chapters/ingress/README.md)
5. [Debugging & Useful Commands](chapters/debug/README.md)
8. [Helm](#Helm)

[ [SoloProject](#d) ]  [ [Nana Project Review](chapters/nana/README.md) ]  [ [Theory](#Theory) ]
  
 
    - [Sample Config File](#Sample-Config-File)
    - [NameSpace](#NameSpace)  

  
  

![](https://www.praqma.com/images/stories/kubernetes-sami.jpg)  
  
### Introduction  
  
Kubernetes is a container orchastration tool that is extemely popular in development, devops and infrastructure. It can be complicated to learn as it is built upon the concepts of containerisation (docker), Containers can be thought of as a strip down vm, lightweight and designed to run a single app only. Kubernetes is meant to manage multiple containers at scale. 
  
In short kubernetes lets you deploy a full end to end digital service, such as a database, front end and back end all in the one ecosystem. It allows for scaling accross multiple machines on prem or on the cloud and lets you manage routing such as ingress and namespaces.   
  
These notes are not designed as a tutorial, rather go to notes from resources including Nana's youtube, Kubernetes documentation and blogs.   
  


### High Level Steps
      
1. Start Minikube 
2. Deploy secrets,configmap with kubectl 
3. Deploy config
  
- Decide what you want to build
- Read docker requirements for the images you use
- create config files
- spin up   


```sh
minikube start --vm-driver=hyperkit     # starts minikube

kubectl create deployment my-depl --image=nginx 
OR
kubectl apply -f [config.yaml]          # applies config 

kubectl edit deployment my-depl         # edits active deployment
  
kubectl exec -it[pod name] -- bin/bash  # jumps into pod
  
kubectl delete deployment nginx-depl    # deletes 

```
    
  





# Helm  
  

Package Manager for Kubernetes, like `apt/yum/homebrew` for kubernetes.   
  
To package collection of YAML files and distribute to public registry.  
  
For Elastic Stack for logging you would need:  
  
- Stateful set  
- config map 
- K8s User with permissions  
- Secret  
- Services







 


  
# Theory  
  
  


[Navigation](#Navigation)  
      
  


  **DEPLOYMENT** manages a 
    
  
  **REPLICASET** manages a 
  
    
  **POD** manages a  
    
  **container**  
 

## Config file  
 
- Has Three parts  
  - Metadata
  - spec 
  - status  (auto generated/updated)   
  
## Sample Config File
  

- note the `---` is just seperation .  

```yaml
apiVersion: apps/v1
kind: Deployment
metadata: ---------------------> for 
  name: mongo-express
  labels:
    app: mongo-express
spec:  -------------------------> for 
  replicas: 1
  selector:
    matchLabels:
      app: mongo-express
  template:
    metadata: --------------------> for pods
      labels:
        app: mongo-express
    spec: ------------------------> for pods
      containers:
      - name: mongo-express
        image: mongo-express
        ports:
        - containerPort: 8081
        env:
        - name: ME_CONFIG_MONGODB_ADMINUSERNAME
          valueFrom: ------------------------> from secrets file
            secretKeyRef:
              name: mongodb-secret
              key: mongo-root-username
        - name: ME_CONFIG_MONGODB_ADMINPASSWORD
          valueFrom: 
            secretKeyRef:
              name: mongodb-secret
              key: mongo-root-password
        - name: ME_CONFIG_MONGODB_SERVER
          valueFrom: ------------------------> from config map
            configMapKeyRef:
              name: mongodb-configmap
              key: database_url
---
apiVersion: v1 ------------------------> service in same file 
kind: Service
metadata:
  name: mongo-express-service
spec:
  selector:
    app: mongo-express  ------------------------> Connects to pods of same name
  type: LoadBalancer  
  ports:
    - protocol: TCP
      port: 8081
      targetPort: 8081
      nodePort: 30000

```  

**LABELS** and **SELECTORS**  
 
- Metadata have labels  
- Spec has selectors
  
  
- Metadata at the top applies to the **service**  
- Metadata in the template applies to the **pod**  


**Status** is polled from ETCd,  
  
**Format** is YAML, strict about indendations.  
  
Use Yaml validator online.  
  
**Store** yaml files with your code.  
  
**Template** part has its own metadata and spec part, it applies to pods.

**Template** is the blueprint for your pod.  
  
    

## NameSpace  
    
![](images/default.png)  
*All objects reside in the default namespace unless otherwise set*  

Name spaces are logical partitions within a cluster, if you don't create any all resources go into `default` name space. 


Four kinds of namespaces  

- kube-system `system info` 
- kube-public `kubectl clusterinfo` 
- kube-node-lease `heartbeat` 
- default  `this is where our stuff goes if we haven't created a namespace`     
  
### Use Cases  
  

![](images/namespace.png)  
* each name space in this case represents a function partition*      

- Group by function (Db, Monitoring etc)
- IAM restrict teams to a namespacs
  - limit resources quotas per namespace
- Group by teams (teamA,TeamB) 
    - prevents conflict and overwriting
- BlueGreen Deployment that needs to share resources   
    
## Create  
  

`kubectl create namespace [my namespace]`    
    

    
or apply namespace config file  
  
```yaml
apiVersion: v1
kind: configMap
metadata:
  name: my-app-configmap
  namespace: my-assigned-namespace
data:
  db_url: mysql_service.database  

```  
   
```
kubectl apply -f myconfigmap.yaml
```
    
**Important**  
  
If you try to get your config map, it wont work `kubectl get configmap` because it defaults to default namespace.  
  
run:  
  
`kubectl get configmap -n [target-namespace]`
  
- This has to be applied for all commands and config files  .  
    
## Kubens  
  
Allows you to switch namespace:  
  
`brew install kubectx`  
    
### List namespace   
  
kubens  

### Switch and create namespace  
  
`kubens my-namespace`  
  



## Notes  
  
- Each namespace must define its own config map, secrets 
- Service can be accessed outside of a namespace using the following convention   
  
```yaml
data:
  db_url: mysql_service.other_namespace  
```  
   
The end affix `other_namespace` is the name of the namespace you are trying to get into .  
  
- Some components are global you can see them by running `kubectl api-resources --namespaced=false` 
  - Vol
  - Node 
  
List all namespace resources  
  
`kubectl api-resources --namespaced=true`    
  
  

