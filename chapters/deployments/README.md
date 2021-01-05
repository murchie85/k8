# Deployments
    
## Navigation

**[Return to Main Page](../../README.md)**

  
1. [Create Deployment from Image](#Creating-Deployment-From-Image)
2. [Create Deployment from Configuration File](#Create-Deployment-from-Configuration-File)   
    - [Secrets](#Secrets)       
    - [CONFIG MAP](#CONFIG-MAP)  
    - [Creating your config file](#Creating-your-config-file)  
    - [SSH into pod](#SSH-into-pod)      


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
    

**Ways to Check**  
  

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

```yaml
    spec:
      containers:
      - image: nginx:1.16
```  
- do a get all to see that it gets deleted and a new pod gets created.  
  
  

    
  













# Create Deployment from Configuration File
   

[Navigation](#Navigation)  
   

consider the folowing:  
  
`kubectl create deployment name image option1 option2` 
  
- Using config file is much easier.  
  
### Method  
  
```
kubectl apply -f [file_name] 
```    
  

## Secrets  
  

[Navigation](#Navigation)  
      
  
    
** MUST BE CREATED BEFORE DEPLOYMENT **  
  
  
- In spec you may have env name/value pairs
- Create a `secrets.yaml` file  

```yaml
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

Reference the secret with the following in the `env` section using `valueFrom` and `secretKeyRef`:  
  
** NAME MUST BE THE ENV VAR SPECIFIED IN THE DOCUMENTATION**  

```yaml
        - name: MONGO_INITDB_ROOT_USERNAME
          valueFrom:
            secretKeyRef:
              name: mongodb-secret
              key: mongo-root-username
```  
  



Once populated appropriately apply: 
  
``` 
kubectl apply -f secrets.yaml  
```   
  
Then to get the secret run:  
  
```
kubectl get secret  
```  
  

## CONFIG MAP
  
- Centralises referneces 
- saves updating multiple configs  
  

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: mongodb-configmap
data:
  database_url: mongodb-service
```
    
- Apply  

```
kubectl apply -f mongo-configmap.yaml
```
  
   
### Creating your config file
  

[Navigation](#Navigation)  
      


1. Create a yaml file  
```
touch nginx-deployment.yaml 
```
2. Edit it like the following 

```yaml
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
 
```yaml
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
  
```yaml
apiVersion: v1
kind: Service
metadata:
  name: nginx-service
spec:
  selector:
    app: nginx
  ports:
    - protocol: TCP
      port: 80 ------------------> exposed port
      targetPort: 8080 ----------> container port

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
      
  
**[Return to Main Page](../../README.md)**
