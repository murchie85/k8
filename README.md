# K8 in a Nutshell 
     
    
![](https://www.praqma.com/images/stories/kubernetes-sami.jpg)  


# Navigation
  
1. [Introduction](#Introduction)
2. [Start up minikube](chapters/minikube/README.md)   
3. [Deployments](chapters/deployments/README.md)
4. [External Services & Ingress](chapters/ingress/README.md)
5. [Useful Commands & Debugging](chapters/debug/README.md)
8. [Helm](#Helm)
9. [Theory](chapters/theory/README.md)

[ [SoloProject](#d) ]  [ [Nana Project Review](chapters/nana/README.md) ] 
  

  
### Introduction
  
Kubernetes is a container orchastration tool that is extemely popular in development, devops and infrastructure. It can be complicated to learn as it is built upon the concepts of containerisation (docker), Containers can be thought of as a strip down vm, lightweight and designed to run a single app only. Kubernetes is meant to manage multiple containers at scale. 
  
In short kubernetes lets you deploy a full end to end digital service, such as a database, front end and back end all in the one ecosystem. It allows for scaling accross multiple machines on prem or on the cloud and lets you manage routing such as ingress and namespaces.   
  
These notes are not designed as a tutorial, rather go to notes from resources including Nana's youtube, Kubernetes documentation and blogs.   
  
![k8 image](https://39lxv6m650h1g391z2daj1l1-wpengine.netdna-ssl.com/assets/blog-kubernetes-og.jpg)

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
    
  











 