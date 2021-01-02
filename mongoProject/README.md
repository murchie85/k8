# Mongo Project  
  
All notes taken from Nana's channel [](https://www.youtube.com/watch?v=X48VuDVv0do&t=5147s&ab_channel=TechWorldwithNana)  
  
- Deploy two applications  
- MongoDB and Mongo express  
- Simple web app with DB  
  
Creates a **mongoDB** pod 
With Internal service 

Create mongo **express** deployment 
DB URL for mongoDB so express can connect to it   
Credentials to auth to DB  
Has Deployment config file  
  

### kubectl apply commands in order
    
    kubectl apply -f mongo-secret.yaml
    kubectl apply -f mongo.yaml
    kubectl apply -f mongo-configmap.yaml 
    kubectl apply -f mongo-express.yaml

### kubectl get commands

    kubectl get pod
    kubectl get pod --watch
    kubectl get pod -o wide
    kubectl get service
    kubectl get secret
    kubectl get all | grep mongodb

### kubectl debugging commands

    kubectl describe pod mongodb-deployment-xxxxxx
    kubectl describe service mongodb-service
    kubectl logs mongo-express-xxxxxx

### give a URL to external service in minikube

    minikube service mongo-express-service
