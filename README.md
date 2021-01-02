# K8 in a Nutshell 
    

![k8 image](https://39lxv6m650h1g391z2daj1l1-wpengine.netdna-ssl.com/assets/blog-kubernetes-og.jpg)  
![](https://www.praqma.com/images/stories/kubernetes-sami.jpg)  


# Navigation
  
1. [Full Command Sequence ](#Full-Command-Sequence)
2. [Start up minikube](#Start-up-minikube)   
3. [Creating Deployment From Image](#Creating-Deployment-From-Image) 
  - [Edit Deployment Image](#Edit-Deployment-Image)
  - [SSH into pod](#SSH-into-pod)  
4. [Create and Manage via Configuration File](Create-and-Manage-via-Configuration-File) 
  - [Secrets](#Secrets)
5. [Debug Commands](#Debug-Commands) 
6. [SoloProject]

99. [Theory](#Theory)  
  
  

## Useful Notes   
  
- Don't need to modity replicasets   
- Secrets need to be applied before cluster spins up. 
  


### Full Command Sequence
    

```sh
minikube start --vm-driver=hyperkit

kubectl create deployment nginx-depl --image=nginx 
OR
kubectl apply -f [config.yaml]

kubectl edit deployment nginx-depl  
  
kubectl exec -it[pod name] -- bin/bash 
  
kubectl delete deployment nginx-depl  

```
    
  
  
## Start up minikube
  
 
```
minikube start --vm-driver=hyperkit
```  

### Check minikube status  
  
```sh
minikube status  
``` 
  


**EVERYTHING IS KUBECTL** from here on.  
  
*minikube is just for starting the cluster*  
   

### Check available nodes

- Checks for nodes like other machines  
  
```sh
kubectl get nodes
```
      

### Get kubectl version  
   
- If we see client and server version it's all working ok.  

```sh
kubectl version
```
- Note client ver 
- note server ver 
```sh
Client Version: version.Info{Major:"1", Minor:"15", GitVersion:"v1.15.5", GitCommit:"20c265fef0741dd71a66480e35bd69f18351daea", GitTreeState:"clean", BuildDate:"2019-10-15T19:16:51Z", GoVersion:"go1.12.10", Compiler:"gc", Platform:"darwin/amd64"}

Server Version: version.Info{Major:"1", Minor:"20", GitVersion:"v1.20.0", GitCommit:"af46c47ce925f4c4ad5cc8d1fca46c7b77d13b38", GitTreeState:"clean", BuildDate:"2020-12-08T17:51:19Z", GoVersion:"go1.15.5", Compiler:"gc", Platform:"linux/amd64"}
```
  
  
## get services 
  
- command  
  
```
kubectl get services  
kubectl get all // better
```    
  










  

  
# Creating Deployment From Image
    

[Navigation](#Navigation)  
  

- We don't create pods
- We create the layer above i.e. deployments 
  
  
```sh

# Create a new deployment named nginx-depl that uses nginx image from docker
kubectl create deployment nginx-depl --image=nginx 
```    

- Creates a pod
- Creates a replicaset
- creates a deployment
    

**Check**  
  

```
kubectl get pod
kubectl get depoloyment
kubectl get replicaset
```  
    
    

## Edit Deployment Image

[Navigation](#Navigation)  
 
  
(You only ever need to edit stuff via deployment)  
  
```

kubectl edit deployment nginx-depl 

```
  
- most stuff is auto generated default   
- because how we set it.  
    
- we make a change to ver 
```
    spec:
      containers:
      - image: nginx:1.16
```  
- do a get all to see that it gets deleted and a new pod gets created.  
  
  


## SSH into pod

```
kubectl exec -it[pod name] -- bin/bash
```
  
## Delete Deployment  
  
```
kubectl delete deployment [depl-name]
```  
  
- This will delete replicaset, pod, service   
- Test with `kubectl get all`  
  
    


# Create and Manage via Configuration File
   

[Navigation](#Navigation)  
   

consider the folowing:  
  
`kubectl create deployment name image option1 option2` 
  
- Using config file is much easier.  
  
### Method  
  
```
kubectl apply -f [file_name] 
```    
  

## Secrets
    
** MUST BE CREATED BEFORE DEPLOYMENT **  
  
  
- In spec you may have env name/value pairs
- Create a `secrets.yaml` file  

```YAML
apiVersion: v1
kind: Secret
metadata:
  name: mongodb-secret
type: Opaque
data:
    mongo-root-username:
    mongo-root-password:

```
  
- to generate base 64 encoded.  

```
echo -n 'username' | base64    
```
Once populated appropriately apply
  
``` 
kubectl apply -f secrets.yaml  
``` 

   
### Creating your config file  
  

1. Create a yaml file  
```
touch nginx-deployment.yaml 
```
2. Edit it like the following 

```
apiVersion: apps/v1
kind: Deployment
metadata:
  name: nginx-deployment
  labels:
    app: nginx
spec:
  replicas: 1
  selector:
    matchLabels:
      app: nginx
  template:
    metadata:
      labels:
        app: nginx
    spec:
      containers:
      - name: nginx
        image: nginx:1.16
        ports:
        - containerPort: 8080
```
  
3. Apply  
  
``` 
kubectl apply -f nginx-deployment.yaml  
```  
 
4. Make changes  
  
- edit yaml file 
 
```
replicas: 2
```  
 
- apply again  
  
```
kubectl apply -f nginx-deployment.yaml  
   
```  
  
5. Make service file   

```
touch nginx-service.yaml
```
  
```
apiVersion: v1
kind: Service
metadata:
  name: nginx-service
spec:
  selector:
    app: nginx
  ports:
    - protocol: TCP
      port: 80
      targetPort: 8080

```
  
Targetport **should match** the containerport
  

```
kubectl apply -f nginx-service.yaml
   
```    
    
**Validate** it is listening on the right ports etc  
  
```
kubectl describe service nginx-service.yaml  
```  
     
Check the **pods match up**  
    
```
kubectl get pod -o wide
```  
  
Check current **status**  
  
```
kubectl get deployment nginx-deployment -o yaml 
```





## Create example usage    
```sh 
kubectl create -h  
  
### example usage  
Usage:
  kubectl create deployment NAME --image=image [--dry-run] [options]
```  
      
In the options we can specify more than one replica set.  
  

  
## Debug Commands

[Navigation](#Navigation)  
      

- SSH into pod
- Get Logs 
- Describe pods
- Describe service
- More pod info  
- Get active deployment info 

  
```
kubectl exec -it[pod name] -- bin/bash 
```  

    
```
kubectl logs [podname]
```    
  
```  
kubectl describe pod [podname]  
```
- output  
  
```
  Type    Reason     Age    From               Message
  ----    ------     ----   ----               -------
  Normal  Scheduled  4m25s  default-scheduler  Successfully assigned default/nginx-depl-7fc44fc5d4-ncwkx to minikube
  Normal  Pulling    4m25s  kubelet, minikube  Pulling image "nginx:1.16"
  Normal  Pulled     4m23s  kubelet, minikube  Successfully pulled image "nginx:1.16" in 1.435142631s
  Normal  Created    4m23s  kubelet, minikube  Created container nginx
  Normal  Started    4m23s  kubelet, minikube  Started container nginx
  ```


## Validate it is listening on the right ports etc  
  
```
kubectl describe service nginx-service.yaml  
```  
   
### Check the pods match up  
  
```
kubectl get pod -o wide
```  
  
## Get active deployment info  
  
```
kubectl get deployment nginx-deployment -o yaml
```




















## Useful KUBECTL Commands    

```
kubectl get all    // get everything
  

kubectl delete deployment nginx-depl
```

## Useful MINIKUBE commands    
  

  
```
minikube start
minikube status
minkube stop
minikube delete
minikube dashboard
minkube puase
minikube unpause
```





- minikube output  

```shell 
minikube provisions and manages local Kubernetes clusters optimized for development workflows.

Basic Commands:
  start          Starts a local Kubernetes cluster
  status         Gets the status of a local Kubernetes cluster
  stop           Stops a running local Kubernetes cluster
  delete         Deletes a local Kubernetes cluster
  dashboard      Access the Kubernetes dashboard running within the minikube cluster
  pause          pause Kubernetes
  unpause        unpause Kubernetes

Images Commands:
  docker-env     Configure environment to use minikube's Docker daemon
  podman-env     Configure environment to use minikube's Podman service
  cache          Add, delete, or push a local image into minikube

Configuration and Management Commands:
  addons         Enable or disable a minikube addon
  config         Modify persistent configuration values
  profile        Get or list the current profiles (clusters)
  update-context Update kubeconfig in case of an IP or port change

Networking and Connectivity Commands:
  service        Returns a URL to connect to a service
  tunnel         Connect to LoadBalancer services

Advanced Commands:
  mount          Mounts the specified directory into minikube
  ssh            Log into the minikube environment (for debugging)
  kubectl        Run a kubectl binary matching the cluster version
  node           Add, remove, or list additional nodes

Troubleshooting Commands:
  ssh-key        Retrieve the ssh identity key path of the specified node
  ssh-host       Retrieve the ssh host key of the specified node
  ip             Retrieves the IP address of the specified node
  logs           Returns logs to debug a local Kubernetes cluster
  update-check   Print current and latest version number
  version        Print the version of minikube

Other Commands:
  completion     Generate command completion for a shell

```  
  
# Theory  
  


  **DEPLOYMENT** manages a 
    
  
  **REPLICASET** manages a 
  
    
  **POD** manages a  
    
  **container**  
 

## Config file  
 
- Has Three parts  
  - Metadata
  - spec 
  - status  (auto generated/updated)   
  

```
apiVersion: apps/v1
kind: Deployment
metadata: ---------------------> for service
  name: nginx-deployment
  labels:
    app: nginx
spec:  -------------------------> for deployment
  replicas: 1
  selector:
    matchLabels:
      app: nginx
  template:
    metadata:--------------------> for pods
      labels:
        app: nginx
    spec:------------------------> for pods
      containers:
      - name: nginx
        image: nginx:1.16
        ports:
        - containerPort: 8080
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
  

