
# kubernetes workshop (k8s)

This workshop will use a localhost kubernetes environment called 'minikube', an ideal starting point in getting to know kubernetes.

MacOS:
Install the following (using brew):
> brew cask install docker
> brew install kubectl
> brew cask install virtualbox
> brew cask install minikube

Linux/Windows:


After install run minikube to download an image:
minikube start

After download has finished:
minikube stop

See you thursday!


Kubernetes URL: https://kubernetes.io/
Minikube URL: https://github.com/kubernetes/minikube
              https://kubernetes.io/docs/getting-started-guides/minikube/

So lets get the show on the road.

minikube start

now start kubectl without any parameters:

kubectl

You see there are a lot of commands we can pass to kubectl.
Not all of them will be covered in this workshop.
I hope to give you a good start understanding of the basics so you can explore the others in your own time.

Lets start with the get command.


To see a resource(s) that is/are running on kubernetes you can issue a kubectl 'get' <resource>
There is an 'all' resource with list all resources (as one would expect from a resource with was named 'all').

> kubectl get all

As you can see there are no pods currently running on minikube. Only a special service called kubernetes (TODO: what is this exactly?).

Lets install an example application called 'echoserver'.

kubectl run hello-minikube --image=k8s.gcr.io/echoserver:1.3 --port=8080

This will start a single instance of echoservice and let the container expose port 8080.

Run kubectl get all again and see what it did exactly.

As you can see it did not just create a pod as you might have expected but it als created a deployment and replicaset.





