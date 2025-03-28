### k8s Practice Tests

----

##### Security Contexts - Lab

1-
What is the user used to execute the sleep process within the ubuntu-sleeper pod?

In the current(default) namespace. : `root`
```BASH 
k exec ubuntu-sleeper -c ubuntu -- ps -aux
USER         PID %CPU %MEM    VSZ   RSS TTY      STAT START   TIME COMMAND
root           1  0.0  0.0   2696  1180 ?        Ss   22:10   0:00 sleep 4800
root          74  0.0  0.0   7888  4256 ?        Rs   22:22   0:00 ps -aux
```


2-
Edit the pod `ubuntu-sleeper` to run the sleep process with user ID 1010.

Note: Only make the necessary changes. Do not modify the name or image of the pod.

```BASH 

k delete pod ubuntu-sleeper --force

k edit pod ubuntu-sleeper

# Please edit the object below. Lines beginning with a '#' will be ignored,
# and an empty file will abort the edit. If an error occurs while saving this file will be
# reopened with the relevant failures.
#
apiVersion: v1
kind: Pod
metadata:
  creationTimestamp: "2025-03-27T22:10:53Z"
  name: ubuntu-sleeper
  namespace: default
  resourceVersion: "864"
  uid: 9b49b335-7a06-4da1-ac87-7efbd586fcb7
spec:  
  securityContext:
    runAsUser: 1010
  containers:
  - command:
    - sleep
    - "4800"
    image: ubuntu
    imagePullPolicy: Always
    name: ubuntu
.
.
.

# Once done, 
kubectl apply -f ubuntu-sleeper.yaml

```


3-
A Pod definition file named multi-pod.yaml is given. With what user are the processes in the web container started?

The pod is created with multiple containers and security contexts defined at the Pod and Container level.

```BASH 
apiVersion: v1
kind: Pod
metadata:
  name: multi-pod
spec:
  securityContext:
    runAsUser: 1001
  containers:
  -  image: ubuntu
     name: web
     command: ["sleep", "5000"]
     securityContext:
      runAsUser: 1002

  -  image: ubuntu
     name: sidecar
     command: ["sleep", "5000"]
```


4-
With what user are the processes in the sidecar container started?

The pod is created with multiple containers and security contexts defined at the Pod and Container level. : `1001`
```BASH 
cat multi-pod.yaml |less

apiVersion: v1
kind: Pod
metadata:
  name: multi-pod
spec:
  securityContext:
    runAsUser: 1001
  containers:
  -  image: ubuntu
     name: web
     command: ["sleep", "5000"]
     securityContext:
      runAsUser: 1002

  -  image: ubuntu
     name: sidecar
     command: ["sleep", "5000"]
```


5-

Update pod ubuntu-sleeper to run as Root user and with the SYS_TIME capability.

Note: Only make the necessary changes. Do not modify the name of the pod.

```bash

```


###### Conclusion