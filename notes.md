In the official documentation, search for `Cheatsheet`

#### Practice Test - Services

1- How many services exist in the system? : `1`
That is a default service created by Kubernetes at launch.
```BASH
kubectl get services
##### OUTPUT
NAME         TYPE        CLUSTER-IP   EXTERNAL-IP   PORT(S)   AGE
kubernetes   ClusterIP   10.43.0.1    <none>        443/TCP   12m
```

2- What is the targetPort configured on the kubernetes service? : `6443`
```BASH
kubectl describe service kubernetes 
Name:              kubernetes
Namespace:         default
Labels:            component=apiserver
                   provider=kubernetes
Annotations:       <none>
Selector:          <none>
Type:              ClusterIP
IP Family Policy:  SingleStack
IP Families:       IPv4
IP:                10.43.0.1
IPs:               10.43.0.1
Port:              https  443/TCP
TargetPort:        6443/TCP
Endpoints:         192.17.55.6:6443
Session Affinity:  None
Events:            <none>
```

4- How many labels are configured on the kubernetes service? : `2`
```BASH
```

5- How many Endpoints are attached on the kubernetes service? : `1`
```BASH
```


6- How many Deployments exist on the system now? : `1`
```BASH
kubectl get deployments
NAME                       READY   UP-TO-DATE   AVAILABLE   AGE
simple-webapp-deployment   4/4     4            4           23s

```


- What is the image used to create the pods in the deployment? : `kodekloud/simple-webapp:red`
```BASH
 kubectl describe deployment simple-webapp-deployment
```

XXX- Create a new service to access the web application using the service-definition-1.yaml file. : `DONE`

```BASH

# Create the service file and paste the content bellow in it:

nano service-definition-1.yaml

Name: webapp-service
Type: NodePort
targetPort: 8080
port: 8080
nodePort: 30080
selector:
  name: simple-webapp

---
apiVersion: v1
kind: Service
metadata:
  name: webapp-service
  namespace: default
spec:
  ports:
  - nodePort: 30080
    port: 8080
    targetPort: 8080
  selector:
    name: simple-webapp
  type: NodePort

# build service file
kubectl apply -f ./service-definition-1.yaml

```




#### Practice Test - Namespaces

```BASH
### NOTES
# Pods in all namespaces
kubectl get pods --all-namespaces

## get pods of a specific namespace
kubectl get pods --namespace=dev

# set the current context namespace
kubectl config set-context $(kubectl config current-context) --namespace=dev
```

1- How many Namespaces exist on the system? : `10`
```BASH
kubectl get namespaces --no-headers | wc -l
```

- How many pods exist on the research namespace ? : `2`
```bash
kubectl get pods --namespace=research
```

2- Create a POD in the finance namespace : `2`
```BASH
# Specs
# Name: redis
# Image name: redis

# kubectl get namespaces --no-headers | wc -l
kubectl run redis --image=redis --dry-run=client -o yaml > redis-definition.yaml
# apiVersion: v1
# kind: Pod
# metadata:
#   creationTimestamp: null
#   labels:
#     run: redis
#   name: redis
#   namespace: finance
# spec:
#   containers:
#   - image: redis
#     name: redis
#     resources: {}
#   dnsPolicy: ClusterFirst
#   restartPolicy: Always
# status: {}

kubectl create -f redis-definition.yaml
```


3- Which namespace has the blue pod in it? : `marketing`
```BASH 
kubectl get pods  --all-namespaces| grep blue
# marketing       blue                                      1/1     Running            0                43m
```

4- What DNS name should the Blue application use to access the database db-service in its own namespace - marketing?
```BASH

# kubectl describe pod blue --namespace=marketing
# <service-name>.<namespace>.svc.cluster.local



```


kubectl get pods  --all-namespaces| grep blue



XXX- QUESTION : `ANSWER`

```BASH

```








XXX- QUESTION : `ANSWER`

```BASH
### NOTES
```

```BASH

```



XXX- QUESTION : `ANSWER`

```BASH

```


XXX- QUESTION : `ANSWER`

```BASH

```








XXX- QUESTION : `ANSWER`

```BASH
### NOTES
```



XXX- QUESTION : `ANSWER`

```BASH

```





XXX- QUESTION : `ANSWER`

```BASH

```







XXX- QUESTION : `ANSWER`

```BASH

```






```BASH

```

214685279
214685379

559130864

728403916

615298472

832741560






-------





#### 173. Role Based Access Controls

Kubernate allow cluster admin to grant some permissions to users in the cluster

```bash
kubectl get roles
kubectl get rolebindings
kubectl describe role developer
```

To get details about rolebindings
```bash
kubectl describe rolebinding devuser-developer-binding
```

###### Check Access

Alternatively, kubernates allow us to have access or not to certains elements in the cluster
```bash
`kubectl auth can-i create deployments
`kubectl auth can-i delete nodes
```

After as admin you set some access to users, you can check if the configuration is well done by checking access of your users like this

```bash
`kubectl auth can-i delete nodes --as dev-user --namespace test
```

###### Resource Names

This feature allow us to group resource (pod, node, replicasets) in a group


###### Practice Test

[174](practice-tests/174.md)

```developer-role.yaml
apiVersion: rbac.authorization.k8s.io/v1
kind: Role
metadata:
    name: develoer
rules:
-   apiGroups: [""]
    resources: ["pods"]
    verbs: ["get", "create", "update"]
    resourceNames: ["blue", "orange"]
```








```bash

```




