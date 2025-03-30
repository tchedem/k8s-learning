Here, we will work on network policies in more details

![alt text](resources/image-3.png)
*credit kodekloud*

our goal is to protect the db pod and make sure that it doesn't allow connection form any other pods exerpt from the API pod on port 3306


the resource we want to protect is the db pod. So we should defile the policyTypes from the perspective of the db pod

![policiy rhat block all traffic to the bd pod excerpt the api pod traffic](resources/image-4.png)
*credit kodekloud*

to specify from what namespace is allowd to reach the db pod

![alt text](resources/image-5.png)
*credit kodekloud*


![all pods in the namespace can reach the bd pod but pod outside of that namespace won't be able to reach it](resources/image-6.png)
*credit kodekloud*

if not specified 

![if we have an external srv that want to reach a pod](resources/image-7.png)
*credit kodekloud*

those are the 3 selector available on the from (egress section)

interpret elemnents under the -from section

![alt text](resources/image-8.png)
*credit kodekloud*

### egress
instead of a backup srv initiating a backup operation,  we write a job that trigger the operation from the db pod and send the generated file to the backeup server.

![network-policy with Egress](resources/image-9.png)
*credit kodekloud*

all selector viewed on the ingress are still available here