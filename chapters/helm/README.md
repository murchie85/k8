# Helm 
    
![](https://miro.medium.com/max/800/0*j5qOv3O6AE-xnUGD.png)

**[Return to Main Page](../../README.md)**  
  
  
## Navigation  

[Intro](#Intro)

 
  
## Intro  
  

**Package Manager** for Kubernetes, like `apt/yum/homebrew` for kubernetes.   
  
To package collection of YAML files and distribute to public registry.  
  
For Elastic Stack for logging you would need:  
  
- Stateful set (persistent storage)
- config map 
- K8s User with permissions  
- Secret  
- Services  
  
Setting up all the bits can be tricky, other people would have to do the same though - it makes sense that someone packaged the whole bundle up.  
  
**Helm-Chart** - a collection of config files for a full kubernetes managed product.  
  
  
**helm hub:**  

 - Can create your own helm charts  
 - Push to Helm repository (like dockerhub)  
 - Download existing ones  
 - Private repos also exist  


Look for helmcharts - `helm search <keyword> `   

Helm is a *Templating engine*   
  
- Instead of writing lots of yaml files for pods that are similar (i.e. microservices)  
- Define *blueprint*  
	- Dynamic values replaced by placeholders  

```yaml
apiVersion: v1
kind: pod
metadata: 
	name: {{ .Values.name}}
spec:
  containers:
  	- name: {{.Values.container.name}}
  	  image: {{Values.container.image}}
  	  port: {{Values.container.port}}

```   
  


  






**[Return to Main Page](../../README.md)**  