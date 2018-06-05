
# kubernetes workshop (k8s)

This workshop will use a localhost kubernetes environment called 'minikube', an ideal starting point in getting to know kubernetes.


## Installation:

I only tried it on a macbook pro without any problems. Linux and windows user have to try and get it installed themselves.


### MacOS:
Install the following (using brew):
```
brew cask install docker
brew install kubectl
brew cask install virtualbox
brew cask install minikube
```

### Linux/Windows:
- Docker: https://www.docker.com/community-edition
- kubectl: https://kubernetes.io/docs/tasks/tools/install-kubectl/
- VirtualBox: https://www.virtualbox.org/wiki/Downloads
- minikube: https://github.com/kubernetes/minikube


## Exploring kubernetes with minikube

So lets get the show on the road.
```
minikube start
```
This will start minikube locally.


### kubectl

Now start kubectl without any parameters:
```
kubectl
```
You see there are a lot of commands we can pass to kubectl.
Not all of them will be covered in this workshop.
I hope to give you a good start understanding of the basics so you can explore the others in your own time.


### get command

Lets start with the get command.

If you issue a get command without any extra parameters then you get a list of resources which you can interact with using kubectl.
```
kubernetes get
```
Just as all the commands, there are also a lot of resources. You are gonna see a few of them in this workshop. The others I'll leave up to you to explore.

To see a resource(s) that is/are running on kubernetes you can issue a kubectl 'get' <resource>
```
kubectl get all
```
As you can see there are no pods currently running on minikube. Only a special service called kubernetes. This is the kubernetes API that is exposed and kubectl uses it to issue commands to the cluster.

I hear you think right now "But where is the master running?". You are correct... it is running but it is hidden from us by a namespace. 
```
kubectl get namespaces
```
The default namespace is the default namespace and all the master resources are running under kube-system. (Side note: The kube-public namespace is used when bootstrapping the cluster by kubeadm).

To see what is hidden behind a namespace just pass it along your command:
```
kubectl get all --namespace kube-system
```

Not lets install an example application called 'echoserver'.
```
kubectl run hello-minikube --image=k8s.gcr.io/echoserver:1.3 --port=8080
```
This will start a single instance of echoservice and let the container expose port 8080.

Run kubectl get all again and see what it did exactly.
```
kubectl get all
```
As you can see it created a deployment, replica set and a pod. To be more specific: the deployment created the replica set and the replica set created the pod. You can see this pattern in the 'Controlled By:' field in the get of these resources.

To view only a single type of resource we can pass it as a parameter to the get command. As you may have noticed when listing all the resources there are also aliases for certain resources. If for example we just want to see the pods try the following 3 ways:
```
kubectl get pod
kubectl get pods
kubectl get po
```
As you can see you can use the singular, plural or alias to indicate a resource.

A get command can also output different formats:
```
kubectl get pods -o wide
kubectl get pods -o yaml
kubectl get pods -o json
```
The later two output a lot of details (the specifications or specs in short) about the resource. Have a look. 


### describe command

If you want to see the current state of a resource (for troubleshooting purposes), you can use the describe command.
You can get the details as follows:
```
kubectl describe <resource>/<name> 
```
or
```
kubectl describe <resource> <name>
```
Leaving out the name will list all resources.

Use kubectl to get the pods names (currently only one available) and use it to describe that specific pod.


### explain command

It explains the resource... try it out for the pod.
```
kubectl explain pods
```
Try a few others as well (remember 'kubectl get'?).


### port-forward command

So how do we connect to the pod? We could also use a port-forward to a specific pod. This is sometimes helpful for debug/troubleshooting. (use kubectl get pods to get the name of the pod)
````
kubectl port-forward pod/<name> 8081:8080
````
This will make the echoserver available at http://localhost:8081

Use control+c to quit port forwarding.

As you may have noticed this will make the pod only available to yourself. So how do we make the pod publicly available?


### expose command

So we have deployed our echoserver but how do we connect to it? This can be done with the expose command. 
```
kubectl expose deployment hello-minikube --type=NodePort
```
The expose command created a service for the echoserver so it is reachable. A service is an abstraction that groups one or more of the same pods behind one stable address (internally or externally). Requests are directed to a pod on a round robin basis by default.

There are 4 types of exposure possible: 
* ClusterIP: service IP is only accessable from within the cluster,
* NodePort: the resources are avaible on an public IP + port number,
* LoadBalancer: the resources are available by an external load balancer,
* ExternalName: see: https://akomljen.com/kubernetes-tips-part-1/

On minikube we need can issue the following command to access the service directly in the browser:
```
minikube service hello-minikube
```
Passing '--url' as extra parameter we can retrieve only the URL.


### logs command

Logs can be viewed with this command. 
```
kubectl logs deployment/hello-minikube
```
You can also use '--follow' to see a real time stream of the logs and use a '--tail x' to see the last x lines of the log.

So now onwards to the cool features!


### scale command

When there is a lot of traffic hitting your application you want to scale up. Use kubectl to tell the deployment it requires 4 pods:
```
kubectl scale deploy/hello-minikube --replicas=4
```
If you check with the get command you'll see that kubernetes made 3 other pods available.


### delete command

With the delete command we can delete resources from the cluster. But this are not always as they appear... retrieve the name of a pod with the get command and issue a delete:
```
kubectl delete pod/<name>
```
And do another get pods. So there are only 3 pods left now right? Wrong! When we scaled up to 4 pods we instructed kubernetes to have 4 replicas up and running. The replica set noticed one was terminated and started another pod all by itself. You can also see this in the Events: part when you do a describe on the replica set.


### set command

The set command is used to set some property of a resource. We are gonna use the set command to trigger a rolling update by updating the echoservers image to version 1.4. To see it in action, open an other terminal and start a watch command (brew install watch) or use kubectl itself to watch a resource:
```
watch -n 1 kubectl get pods
```
or
```
kubectl get pods -w
```

Now to start the rolling update:
```
kubectl set image deployment/hello-minikube hello-minikube=k8s.gcr.io/echoserver:1.4
```
Did you see how kubernetes rolled out new versioned pods, waited for them to become up and running, then terminated some old pods and started new pods, repeat until all new required pods are up and running. Down scaling is just as easy as up: just set a lower number of replicas and kubernetes will automatically terminate some pods for you. 


### autoscale command

So wouldn't it be nice if this could be done automatically? Scale up when there is more traffic and scale down when there is less traffic. That would be something... actually that would be something called Horizontal Pod Autoscaler (HPA).

First scale down to 1 replica:
```
kubectl scale deploy/hello-minikube --replicas=1
```

For the autoscale to work on minikube we need to enable the metrics addons:
```
minikube addons enable metrics-server
```

We also need to tell our deployment how much cpu it wants. 
```
kubectl set resources deploy/hello-minikube --requests=cpu=100m --limits=cpu=200m
```
The unit of CPU resources is millicores, which is 1⁄1000 of one core. With one core being equal to 1 vCPU on a supported cloud provider. So the deployment now requests 10% of the cpu core and is limited to 20% of the cpu core.

Now to set up the autoscale: 
```
kubectl autoscale deploy/hello-minikube --cpu-percent=5 --min=1 --max=4
```
This will automatically trigger the auto scaling when the cpu utilisation of all pods in the deployment is above the threshold of 5%. In order for it to kick in we need to create some requests. You can use apache bench (ab) for that. 

Open the watch command again in a different terminal to see the auto scaling in action!
```
watch -n 1 kubectl get hpa
```
And in an other one:
```
watch -n 1 kubectl get pods
```

Now to fire off the requests and generate some load:
```
ab -n 500000 $(minikube service hello-minikube --url)/
```

After a while you see a change and kubernetes will automatically scale up the pods. And after a while, when ab has finished, it will automatically downscale again. This could take 10 minutes or something.


### dashboard

One last thing... if your not much of a CLI-man then kubernetes also has a nice dashboard (now he tells me)... fire it up and take a look around. Most of the things kubectl can do, can also be done in the dashboard.
```
minikube dashboard
```

## ...now what?

Already finished and still have appetite for something more? 

You can try out a tutorial from kubernetes which deploys a Guestbook:
* https://kubernetes.io/docs/tutorials/stateless-application/guestbook/
* https://github.com/kubernetes/examples

Or try to build your own application and load it up in minikube. This might be a good starting point: http://www.baeldung.com/spring-boot-minikube

Try out a new version of the application by doing a canary release: https://kubernetes.io/docs/concepts/cluster-administration/manage-deployment/#canary-deployments

Set up a database: https://kubernetes.io/docs/tasks/run-application/run-single-instance-stateful-application/

Or something more lighter, try this 'management summary': https://cloud.google.com/kubernetes-engine/kubernetes-comic/

## Suggested further reading:

There is a lot of ground not covered because there is a lot of ground to cover. But I hope I gave you a nice tour of the basics so you can go ahead and explore all the other resources yourself. The documentation on the kubernetes website has a lot of information and example, so that is a good starting point. It is not always clear but that is where google comes in handy ;-).

kubectl cheat sheet:
* https://kubernetes.io/docs/reference/kubectl/cheatsheet

Kubernetes: 
* https://kubernetes.io/docs

Minikube: 
* https://github.com/kubernetes/minikube
* https://kubernetes.io/docs/getting-started-guides/minikube/

There are also loads of instructional video's to be found on youtube from Cloud Native Computing Foundation, also from KubeCon EU 2018:
* https://www.youtube.com/channel/UCvqbFHwN-nwalWPjPUKpvTA/featured

