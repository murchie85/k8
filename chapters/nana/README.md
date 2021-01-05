# Nana Project Review 
  
**[Return to Main Page](../../README.md)**  
  
  
## Navigation  


1. [Summary](#Summary)   
2. [Steps](#Steps)   
3. [Flow](#FLOW)
9. [Config Files](#Config-Files)

  
  

  
      
  
## Summary
    
[Navigation](#Navigation)  

This is a recreation of the project created by Nana [here](https://www.youtube.com/watch?v=X48VuDVv0do&t=5057s&ab_channel=TechWorldwithNana)  
    
`MongoDB` backend database 
`MongoExpress` Front end interactive tool  

It is two main config files, one for mongoDB backend with no external access and another for mongo-express which communicates with backend. There is a secrets file and also a configmap which provides a `database_url`.  
  


```md
MONGODB DEPLOYMENT
Container Port: 27017    
  
MONGO EXPRESS
containerPort: 8081  
nodePort: 30000 

---All Values obtained from Dockerhub documentation
```  
  

# Steps    

[Navigation](#Navigation)  
  
**KEY** Always view the docker image documentation on how to set up env variables.   

1. Set up **secrets file**, use echo to base64 to populate. 
    - Modify mongo-deployment file with username and password key  
    - `kubectl apply -f secrets.yaml`  
    - `kubectl get secret`  
   Set up **configmap file**. 
    - Modify mongoexpress file with details
    - `kubectl apply -f mongo-configmap.yaml`  
    - `kubectl get configmap`   

2. Setup **mongodb.yaml** deployment   
    - From documentation it needs to know: 
    - Which creds to authenticate `MONGO_INITDB_ROOT_USERNAME` and `MONGO_INITDB_ROOT_PASSWORD` 
    - `kubectl apply -f mongodb.yaml`    

**Validation**
    - Validate: `kubectl get all | grep mongodb`  
    - Validate Service: `kubectl describe service mongodb-service`
    - Validate IP matches Pod IP `kubectl get pod -o wide`   

3. Setup **Mongoexpress** service  
    - From documentation it needs to know: 
    - Which Database to connect to: `ME_CONFIG_MONGODB_SERVER`
    - Which creds to authenticate `ME_CONFIG_MONGODB_ADMINUSERNAME` and `ME_CONFIG_MONGODB_ADMINPASSWORD`

    - Create express service 
      - Add type: `loadBalancer` 
  
    - `kubectl apply -f mongo-express.yaml` 
    - Note your service isn't default `clusterIP` its `loadBalancer`  
    - `kubectl logs mongo-express-pod`  check for listening and `db connected`  
    - `minikube service mongo-expresss-service` to go ahead and connect.  


**Now it should Run**  
    

## FLOW      

[Navigation](#Navigation)  
   
- When you test by adding a database:  

`browser` --> `mongo-express external service` --> `mongoDB internal service` --> `mongoDB pod`
 
  


## Config Files

[Navigation](#Navigation)  
  
### secrets.yaml
```yaml
apiVersion: v1
kind: Secret
metadata:
    name: mongodb-secret
type: Opaque
data:
    mongo-root-username: dXNlcm5hbWU=
    mongo-root-password: cGFzc3dvcmQ=

``` 
 
### mongodb.yaml  

```yaml 
apiVersion: apps/v1
kind: Deployment
metadata:
  name: mongodb-deployment
  labels:
    app: mongodb
spec:
  replicas: 1
  selector:
    matchLabels:
      app: mongodb
  template:
    metadata:
      labels:
        app: mongodb
    spec:
      containers:
      - name: mongodb
        image: mongo
        ports:
        - containerPort: 27017
        env:
        - name: MONGO_INITDB_ROOT_USERNAME
          valueFrom:
            secretKeyRef:
              name: mongodb-secret
              key: mongo-root-username
        - name: MONGO_INITDB_ROOT_PASSWORD
          valueFrom: 
            secretKeyRef:
              name: mongodb-secret
              key: mongo-root-password
---
apiVersion: v1
kind: Service
metadata:
  name: mongodb-service
spec:
  selector:
    app: mongodb
  ports:
    - protocol: TCP
      port: 27017
      targetPort: 27017
```
  
## mongo-express.yaml  
  
```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: mongo-express
  labels:
    app: mongo-express
spec:
  replicas: 1
  selector:
    matchLabels:
      app: mongo-express
  template:
    metadata:
      labels:
        app: mongo-express
    spec:
      containers:
      - name: mongo-express
        image: mongo-express
        ports:
        - containerPort: 8081
        env:
        - name: ME_CONFIG_MONGODB_ADMINUSERNAME
          valueFrom:
            secretKeyRef:
              name: mongodb-secret
              key: mongo-root-username
        - name: ME_CONFIG_MONGODB_ADMINPASSWORD
          valueFrom: 
            secretKeyRef:
              name: mongodb-secret
              key: mongo-root-password
        - name: ME_CONFIG_MONGODB_SERVER
          valueFrom: 
            configMapKeyRef:
              name: mongodb-configmap
              key: database_url
---
apiVersion: v1
kind: Service
metadata:
  name: mongo-express-service
spec:
  selector:
    app: mongo-express
  type: LoadBalancer  
  ports:
    - protocol: TCP
      port: 8081
      targetPort: 8081
      nodePort: 30000
```  
  
**[Return to Main Page](../../README.md)**  