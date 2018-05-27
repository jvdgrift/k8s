
# kubernetes workshop (k8s)

This workshop will use a localhost kubernetes environment called 'minikube', an ideal starting point in getting to know kubernetes.

## Installation:
MacOS:
Install the following (using brew):
```
$ brew cask install docker
$ brew install kubectl
$ brew cask install virtualbox
$ brew cask install minikube
```
Linux/Windows:


After install run minikube to download an image:
```
$ minikube start
```
After download has finished:
```
$ minikube stop
```
See you thursday!


Kubernetes URL: 
* https://kubernetes.io/

Minikube URL: 
* https://github.com/kubernetes/minikube
* https://kubernetes.io/docs/getting-started-guides/minikube/

## Exploring kubernetes with minikube

So lets get the show on the road.
```
$ minikube start
```
This will start minikube locally.




### kubectl

Now start kubectl without any parameters:
```
$ kubectl
```
You see there are a lot of commands we can pass to kubectl.
Not all of them will be covered in this workshop.
I hope to give you a good start understanding of the basics so you can explore the others in your own time.

### get command

Lets start with the get command.

If you issue a get command without any extra parameters then you get a list of all resources available to kubernetes.
```
$ kubernetes get
```
Just as all the commands, there are also a lot of resources. You are gonna see a few of them in this workshop. The others I'll leave up to you to explore.

To see a resource(s) that is/are running on kubernetes you can issue a kubectl 'get' <resource>
There is an 'all' resource with list all resources (as one would expect from a resource with was named 'all').
```
$ kubectl get all
```
As you can see there are no pods currently running on minikube. Only a special service called kubernetes. This is the kubernetes API that is exposed and kubectl uses it to issue commands to the cluster.

I hear you think right now "But where is the master running?". You are correct... it is running but it is hidden from us by a namespace. 
```
$ kubectl get namespaces
```
The default namespace is the default namespace and all the master resources are running under kube-system. (Side note: The kube-public namespace is (was?) used when bootstrapping the cluster by kubeadm).

Lets install an example application called 'echoserver'.
```
$ kubectl run hello-minikube --image=k8s.gcr.io/echoserver:1.3 --port=8080
```
This will start a single instance of echoservice and let the container expose port 8080.

Run kubectl get all again and see what it did exactly.
```
$ kubectl get all
```
As you can see it did not just create a pod as you might have expected but it als created a deployment and replicaset.

To view only a single type of resource we can pass it as a parameter to the get command. As you may have noticed when listing all the resources there are also aliases for certain resources. If for example we just want to see the pods try the following 3 ways:
```
$ kubectl get pod
$ kubectl get pods
$ kubectl get po
```
As you can see you can use the singular, plural or alias to indicate a resource.

A get command can also output different formats:
```
$ kubectl get pods -o wide
$ kubectl get pods -o yaml
$ kubectl get pods -o json
```
The later two output a lot of details (the specifications or specs in short) about the resource. Ignore it or have a look. We'll get to the details later on...

### describe command

If you want to see the current state of a resource (for troubleshooting purposes), you can use the describe command.
You can get the details as follows:
```
$ kubectl describe <resource>/<name> 
```
or
```
$ kubectl describe <resource> <name>
```
Leaving out the name will list all resources.

Use kubectl to get the pods names (currently only one available) and use it to describe that specific pod.

### explain command

It explains the resource... try it out for the pod.
```
$ kubectl explain pods
```
Try a few others as well (remember 'kubectl get' lists all the available resources).

### expose command

So we have deployed our echoserver but how do we connect to it? This can be done with the expose command. The expose command will create a service for it so it is reachable. 











### 



### edit command

With edit you can make changes to resources. Lets say we want to update the echoservice to version 1.4.
