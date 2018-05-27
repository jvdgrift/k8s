
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

### Get command

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
As you can see there are no pods currently running on minikube. Only a special service called kubernetes (TODO: what is this exactly?).

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






