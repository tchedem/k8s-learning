
#### Installation process 

```BASH 
### ETCD Installation 

# Download biianries 
curl -L https://github.com/etcd-io/etcd/releases/download/v3.3.11/etcd-v3.3.11-linux-amd64.tar.gz -o etcd-v3.3.11-linux-amd64.tar.gz

# Extract and change property name
tar xzvf etcd-v3.3.11-linux-amd64.tar.gz
mv etcd-v3.3.11-linux-amd64 etcd
cd etcd

# Move etcd and etcdctl binaries to /usr/local/bin
sudo mv etcd/etcd /usr/local/bin/
sudo mv etcd/etcdctl /usr/local/bin/

# Check that the binaries are globally accessible and the correct version is installed
etcd --version
etcdctl --version

# create a dedicate user and directory to ETCD
sudo useradd -r -s /sbin/nologin etcd
sudo mkdir -p /var/lib/etcd
sudo chown -R etcd:etcd /var/lib/etcd
```


Create a Systemctl service file `sudo nano /etc/systemd/system/etcd.service`

```BASH
[Unit]
Description=etcd key-value store
Documentation=https://github.com/coreos/etcd
After=network.target

[Service]
User=etcd
Type=notify
ExecStart=/usr/local/bin/etcd \\
--name ${ETCD_NAME} \\
--cert-file=/etc/etcd/kubernetes.pem \\
--key-file=/etc/etcd/kubernetes-key.pem \\
--peer-cert-file=/etc/etcd/kubernetes.pem \\
--peer-key-file=/etc/etcd/kubernetes-key.pem \\
--trusted-ca-file=/etc/etcd/ca.pem \\
--peer-trusted-ca-file=/etc/etcd/ca.pem \\
--peer-client-cert-auth \\
--client-cert-auth \\
--initial-advertise-peer-urls https://${INTERNAL_IP}:2380 \\
--listen-peer-urls https://${INTERNAL_IP}:2380 \\
--listen-client-urls https://${INTERNAL_IP}:2379,https://127.0.0.1:2379 \\
--advertise-client-urls https://${INTERNAL_IP}:2379 \\
--initial-cluster-token etcd-cluster-0 \\
--initial-cluster controller-0=https://${CONTROLLER0_IP}:2380,controller-1=https://${CONTROLLER1_IP}:2380 \\
--initial-cluster-state new \\
--data-dir=/var/lib/etcd
Restart=always
RestartSec=5s
LimitNOFILE=40000

[Install]
WantedBy=multi-user.target
```

Enable `etcd` service

```BASH
sudo systemctl daemon-reload
sudo systemctl enable etcd
sudo systemctl start etcd
``` 

--- 

```BASH
# Run ETCD Service 
./etcd 

# Set a value in ETCD 
./etcdctl set key1 value1

# Get a specific key value
./etcdctl get key1

# To get help, run this command without any parameter option or flag
./etcdctl

# For the manuel setup, we should create a service file




```



#### Create a pod

```BASH
kubectl run nginx
kubectl run nginx --image=nginx 
```





1.  Kube Proxy
Kube-proxy is a service witch run on each node in the k8s cluster. it's job is to look for new services and one, he discover one, his job is to use IPTables rules to forward the traffic

-> Install it in your cluster

21. Recap : Pod

Single Node Setup 
Multi Node Setup

Containerss are encapsulated into a K8s object norm as POD (Single Instance of an APplication)

kubectl : Deploy a docker container by creating a POD



#### Practice Test 1 - POD

1- How many pods do we have in the default namespace : 0
```BASH
kubectl get pods
# kubectl get pods --all-namespaces
# kubectl get pods --namespace=default --no-headers | wc -l
```

2- Create a new pod with the nginx image. : [OK]
```BASH
kubectl run nginx --image=nginx
```

3- How many pods do we actually have ? : 4
```BASH
kubectl get pods --no-headers | wc -l
```

4- What is the name of the image used to create one of the pods ? : busybox
```BASH
kubectl describe pods newpods-2892l
```

5- Which nodes are these pods placed on ? : controlplane
```BASH
kubectl get pods -o wide 
```

6- How many containers are part of the pod webapp? : 2
```BASH
kubectl describe pod webapp
```

7- What images are used in the new webapp pod? : `BUSYBOX`
```BASH
kubectl describe pod webapp
```

8- What is the state of the container agentx in the pod webapp? : Error or Waiting
```BASH
kubectl describe pod webapp
```

9- Why do you think the container agentx in pod webapp is in error? : "A docker image with this name doesn't exist
```BASH
```

10- What does the READY column in the output of the kubectl get pods command indicate? :  Running Containers in POD/Total containers in POD
```BASH
```

11- Delete the webapp Pod. : [OK]
```BASH
kubectl delete pod webapp
```

12- Create a new pod with the name redis and the image redis123 : [OK]
```BASH
kubectl run redis --image=redis123
kubectl run redis --image=redis123 -o yaml > redis-definition.yaml

kubectl create -f redis-definition.yaml

kubectl run redis --image=redis123 --dry-run=client -o yaml > redis-definition.yaml
----pod_k8s.yaml
apiVersion: v1
kind: Pod
metadata:
  name: redis-img
  labels:
    app: myapp
    type: front-end-server

spec:
  containers:
    - name: redis
      image: redis123


kubectl apply -f redis-pod.yaml

kubectl get pods
```

Now change the image on this pod to redis. Once done, the pod should be in a running state.
```BASH
kubectl edit pod redis
# Then, you just need to save it
```



#### Practice Test 2 - ReplicaSets (Replication Controller)

Replication controller is the older technology that's been replaced by the Replica Set Replica Set is the new recommended way to make replication

Now, let look at how we create a replication controller
```BASH
apiVersion: v1
kind: ReplicationController
metadata: 
  name: myapp-rc
  labels:
    app: myapp
    type: front-end
  
spec: # Replication Conntroller
  - template:
  # POD definition
    metadata:
      name: redis-img
      labels:
        app: myapp
        type: front-end-server

    spec:
      containers:
        - name: redis
          image: redis123

  replicas: 3

```

To run the replication cotroller, use the command bellow
```BASH
kubectl create -f rc-definition.yml
```

TO re the list of running replication controller
```BASH
kubectl get replicationcontroller
```


**Now let's look at replicaset**


Now, let look at how we create a replicaset
```BASH
apiVersion: apps/v1
kind: ReplicationSet
metadata: 
  name: myapp-replicaset
  labels:
    app: myapp
    type: front-end
  
spec: # Replication Conntroller
  - template:
  # POD definition
    metadata:
      name: redis-img
      labels:
        app: myapp
        type: front-end-server

    spec:
      containers:
        - name: redis
          image: redis123

  replicas: 3
  selector: # help to identify what pod faul under it
    matchLabels: 
      type: front-end
  # It is the major diference between RC and replicaset
  # replicaset can manage pod that are not part of the replicaset creation

```

How to scale replicaset ?
1st-  undate the number of `replicas` in the definition file
kubectl replace -f replicaset-definition.yml

2nd- 
kubectl scale --replicate=6 -f replicaset-definition.yml

kubectl scale --replicate=6 -f replicaset myapp-replicaset


```BASH 
# commands
kubectl create -f replicaset-definition.yml
kubectl get replicaset
kubectl delete replicaset myapp-replicaset
kubectl replace -f replicaset-definition.yml
kubectl scale -replicas=6 -f replicaset-definition.yml
```

------



1- How many pods do we have in the default namespace : 0
```BASH
kubectl get pods
# kubectl get pods --all-namespaces
# kubectl get pods --namespace=default --no-headers | wc -l
#  kubectl get pod --no-headers | grep -v "o resource" | wc -l
#  kubectl get pod --namespace default --no-headers | grep -v "o resource" | wc -l
```

2- How many ReplicaSets do you see? : `0`
```BASH
kubectl get replicaset
```

3- How about now? How many ReplicaSets do you see? : `1`
```BASH
kubectl get replicaset
```

4- How many PODs are DESIRED in the new-replica-set? : 4
```BASH
kubectl describe replicaset new-replica-set
.
.
Replicas:     4 current / 4 desired
.
.

kubectl get replicaset
NAME              DESIRED   CURRENT   READY   AGE
new-replica-set   4         4         0       34s
```

5- What i sthe name of the image used in the replicaset containers ? : `busybox777`
```BASH
kubectl describe replicaset new-replica-set
.
.
   busybox-container:
    Image:      busybox777
.
.
```


6- How many PODs are READY in the new-replica-set? : `0`
```BASH
kubectl get replicaset
NAME              DESIRED   CURRENT   READY   AGE
new-replica-set   4         4         0       10m
```

7- Why do you think the PODs are not ready? : `busybox777 image doen't exist`
```BASH
kubectl describe pod pod-name
```

8- Delete on of the pod : ``
```BASH
kubectl delete pod new-replica-set-vtxj7
```


9- How many PODs exist now? : `4`
```BASH
# The deleted pod is recreated automatically by the replicaset
```

10- Why do we still have 4 pod ? : `ReplicaSet ensures that desired number of PODs always run`

11- Create a ReplicaSet using the replicaset-definition-1.yaml file located at /root/. : `ANSWER`
```BASH

# hint commands
kubectl api-resources | grep replicaset
replicasets                         rs           apps/v1                           true         ReplicaSet

kubectl explain replicaset|grep VERSION
VERSION:    v1

# Fix the replicaset-definition-1.yaml file apiVersion
# And, cat the file 

$ cat replicaset-definition-1.yaml

apiVersion: apps/v1
kind: ReplicaSet
metadata:
  name: replicaset-1
spec:
  replicas: 2
  selector:
    matchLabels:
      tier: frontend
  template:
    metadata:
      labels:
        tier: frontend
    spec:
      containers:
      - name: nginx
        image: nginx

# 

```

12- Fix the issue in the replicaset-definition-2.yaml file and create a ReplicaSet using it. : ``
```BASH

# Fix the replicaset-definition-2.yaml  file and cat it

cat replicaset-definition-2.yaml 
apiVersion: apps/v1
kind: ReplicaSet
metadata:
  name: replicaset-2
spec:
  replicas: 2
  selector:
    matchLabels:
      tier: nginx
  template:
    metadata:
      labels:
        tier: nginx
    spec:
      containers:
      - name: nginx
        image: nginx


kubectl create -f replicaset-definition-2.yaml 

```


14- Fix the original replica set new-replica-set to use the correct busybox image. : ``
```BASH

kubectl edit replicaset new-replica-set 
# DONE 

kubectl delete pods $(kubectl ge pods --no-headers | awk '{print $1}')
# DONE

```


15- Scale the ReplicaSet to 5 PODs. : ``
```BASH

# method 1
kubectl scale replicaset new-replica-set --replicas=5

# method 2
kubectl get replicaset
kubectl edit replicaset new-replica-set

```


16- Now scale the ReplicaSet down to 2 PODs. : ``
```BASH
kubectl edit replicaset new-replica-set
# cahnge the value of replicas
```

-----------------

**Practice Test - Deployments**


4- How many Deployments exist on the system now? : `1`
```BASH
kubectl get deployments
```



5- How many ReplicaSets exist on the system now? : `1`
```BASH

```



6- How many PODs exist on the system now? : `4`
```BASH

```



7- Out of all the existing PODs, how many are ready? : `0`
```BASH

```



10- Create a new Deployment using the deployment-definition-1.yaml file located at /root/. : ``
```BASH
kubectl create deployment deployment-personal-definition --image=busybox --replicas=2 -o yaml --dry-run=client > deployment-personal-definition.yaml


cat deployment-definition-1.yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  creationTimestamp: null
  labels:
    app: deployment-definition-1
  name: deployment-1
spec:
  replicas: 2
  selector:
    matchLabels:
      app: deployment-definition-1
  strategy: {}
  template:
    metadata:
      creationTimestamp: null
      labels:
        app: deployment-definition-1
    spec:
      containers:
      - image: busybox
        name: busybox
        resources: {}
status: {}


```



11- Create a new Deployment with the below attributes using your own deployment definition file. : ``

Name: `httpd-frontend`;
Replicas: `3`;
Image: `httpd:2.4-alpine`

```BASH
cat deployment-definition-1.yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  creationTimestamp: null
  labels:
    app: deployment-definition-1
  name: httpd-frontend
spec:
  replicas: 3
  selector:
    matchLabels:
      app: deployment-definition-1
  strategy: {}
  template:
    metadata:
      creationTimestamp: null
      labels:
        app: deployment-definition-1
    spec:
      containers:
      - image: httpd:2.4-alpine
        name: busybox
        resources: {}
status: {}
```


-----

#### Practice Test : Namespace


1- How many Namespaces exist on the system? : `10`
```BASH
kubectl get namespaces --no-headers | wc -l
```

2- How many pods exist in the research namespace? : `ANSWER`
```BASH
kubectl get pods --namespaces=research 
```

3- Create a POD in the `finance` namespace. : ``
```BASH
kubectl run redis --image=redis --namespace=finance
```

4- Which namespace has the blue pod in it? : `marketing`
```BASH
kubectl get pods --all-namespaces|grep blue
marketing       blue                                      1/1     Running            0               24m
```




#### Practice Test 3 - Namespaces

2- How many pods exist in the research namespace? : `2`
```BASH
kubectl get pods --namespace=research
```

3- Create a POD in the finance namespace. : ``
Name: redis
Image name: redis
```BASH
kubectl run redis --image=redis --namespace=finance
```

4- Which namespace has the blue pod in it? : `marketing`
```BASH
kubectl get pods --all-namespaces -o wide | grep blue
```


5- Access the Blue web application using the link above your terminal!! : `ANSWER`
```BASH

```




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
XXX- QUESTION : `ANSWER`
```BASH

```