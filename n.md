Get full list of kubernates resources with they scope (Namespaced or Cluster Scoped)

```bash
kubectl api-resources --namespaced=true

kubectl api-resources --namespaced=false
```

Authorize users to cluster wide resources: `clusterroles` and `clusterrolebinding`


Thing happen in 2 times
 first we create the role 
 second we associate users to those roles

```bash
kubectl create -f cluster-admin-role-binging.yaml
```
We can create `cluster roles` for Namespaced resources too. 
Then user can have access to resources like `pods, deployments` in a set of cluster

Claims ???