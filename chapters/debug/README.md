# Useful Commands and Debugging     
  
![](https://miro.medium.com/max/1121/1*Rd7EUpPf7qnlthT5XEL5zg.png)

**[Return to Main Page](../../README.md)**    

  
## Navigation   

1. [Useful Stuff](#Useful-Stuff)
2. [Debug Commands](#Debug-Commands)



# Useful Stuff
    
[Navigation](#Navigation)  
  
- Don't need to modity replicasets   
- Secrets need to be applied before cluster spins up.   
  
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
    


    
## Useful KUBECTL Commands    

```sh
kubectl get all    // get everything
  

kubectl delete deployment nginx-depl
```  
**SSH into pod:** `kubectl exec -it[pod name] -- bin/bash` 
  

**Delete Deployment:** `kubectl delete deployment [depl-name]` 



## Create example usage    
```sh 
kubectl create -h  
  
### example usage  
Usage:
  kubectl create deployment NAME --image=image [--dry-run] [options]
```  
      
In the options we can specify more than one replica set.  
     

  
## Debug Commands

      
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







**[Return to Main Page](../../README.md)**  
