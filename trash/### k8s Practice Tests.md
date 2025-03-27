### k8s Practice Tests

----

##### RBAC : Role Based Access Control - Lab
https://uklabs.kodekloud.com/topic/practice-test-role-based-access-controls-2/


Inspect the environment and identify the authorization modes configured on the cluster.
Check the kube-apiserver settings. : `Node,RBAC`
```BASH 
# Here, you have 2 alternative to identify the authorization modes configured on the cluster
# One, directly check on kube-apiserver pods via kubectl command
kubectl describe pods kube-apiserver-controlplane -n=kube-system|less
# Second, view the manifest of the kube-apiserver
cat /etc/kubernetes/manifests/kube-apiserver.yaml |less
```

How many roles exist in the default namespace? : `0`
```BASH 
kubectl get roles
```


How many roles exist in all namespaces together?
```BASH 
kubectl get roles --all-namespaces --no-headers | wc -l
12
```

What are the resources the kube-proxy role in the kube-system namespace is given access to? : `configmaps`
```BASH 
# Identify the namespace where kube-proxy is
kubectl get roles -n=kube-system

# Now get the information we need
kubectl describe roles kube-proxy -n=kube-system
PolicyRule:
  Resources   Non-Resource URLs  Resource Names  Verbs
  ---------   -----------------  --------------  -----
  configmaps  []                 [kube-proxy]    [get]
```


What actions can the kube-proxy role perform on configmaps? : `get`
```BASH 
# We can answer this without going typing another command
```


Which of the following statements are true?
```BASH 
# kube-proxy can get details of all configMaps objects by the name `kube-proxy`
```


Which account is the kube-proxy role assigned to? (I don't understand well)
```BASH 
kubectl describe rolebinding kube-proxy -n=kube-system
```


Task
```BASH 

```


Task
```BASH 

```


Task
```BASH 

```


Task
```BASH 

```


Task
```BASH 

```


Task
```BASH 

```


Task
```BASH 

```


Task
```BASH 

```


Task
```BASH 

```


Task
```BASH 

```




























***********************************
***********************************
***********************************
***********************************
TEMPLATE
----

##### Lab_Title - Lab

Task
```BASH 

```


Task
```BASH 

```


Task
```BASH 

```


Task
```BASH 

```

###### Conclusion