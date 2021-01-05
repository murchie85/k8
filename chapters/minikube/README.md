
**[Return to Main Page](../../README.md)**  

## Start up minikube
  
 
```
minikube start --vm-driver=hyperkit
```  

### Check minikube status  
  
```sh
minikube status  
``` 
  

**EVERYTHING IS KUBECTL** from here on. *minikube is just for starting the cluster*    

  
  


### Check available nodes

```sh
kubectl get nodes  # Checks for nodes like other machines 
```
      

### Get kubectl version  
     
```sh
kubectl version   # If we see client and server version it's all working ok
```
- Note the client ver 
- note the server ver   

```sh
Client Version: version.Info{Major:"1", Minor:"15", GitVersion:"v1.15.5", GitCommit:"20c265fef0741dd71a66480e35bd69f18351daea", GitTreeState:"clean", BuildDate:"2019-10-15T19:16:51Z", GoVersion:"go1.12.10", Compiler:"gc", Platform:"darwin/amd64"}

Server Version: version.Info{Major:"1", Minor:"20", GitVersion:"v1.20.0", GitCommit:"af46c47ce925f4c4ad5cc8d1fca46c7b77d13b38", GitTreeState:"clean", BuildDate:"2020-12-08T17:51:19Z", GoVersion:"go1.15.5", Compiler:"gc", Platform:"linux/amd64"}
```
  
  
## get services   
  
```
kubectl get services  
kubectl get all // better
```    
  

  
**[Return to Main Page](../../README.md)**
