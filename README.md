# kube-bootdev

minikube start --extra-config "apiserver.cors-allowed-origins=["http://boot.dev"]"
minikube dashboard --port=63840

Deploying an Image
The kubectl create deployment command will create a "deployment" for us. We'll talk more about the nuances of "deployments" later. But to put it simply, we only need to provide two things:

The name of the deployment (this can be anything, it's used to identify the deployment)
The ID of the Docker image we want to deploy (it would be a full URL if we weren't hosting the image on Docker Hub, which is the default)

kubectl create deployment synergychat-web --image=docker.io/bootdotdev/synergychat-web:latest

This command will deploy a container built from this Docker image to your local k8s cluster.

kubectl get pods
kubectl edit deployment synergychat-web

Run kubectl edit deployment synergychat-web to edit the deployment. This will open the deployment in your default text editor. You should see a big 'ol yaml file. This is the configuration of your deployment. Under the "spec" section, you should see the replicas field set to 1. Change it to 2, save the file, and close the editor.


If you want to set VS Code as your default text editor, you can add:

export EDITOR="code -w"
to your .zshrc or .bashrc file, and restart your terminal. code is the command to open VS Code, and -w tells it to wait until you're done editing the file before continuing.

kubectl get pods
kubectl logs PODNAME
kubectl delete pod PODNAME

kubectl get pods -o wide


This will start a proxy server on your local machine, probably on 127.0.0.1:8001. Assuming that's the host, navigate to http://127.0.0.1:8001/api/v1/namespaces/default/pods in your browser. You should see a big nasty JSON blob that describes the pods that you have running.
kubectl proxy
http://127.0.0.1:8001/api/v1/namespaces/default/pods


https://kubernetes.io/docs/concepts/workloads/controllers/deployment/


Take a look at the YAML file for your current deployment in the CLI:
kubectl get deployment synergychat-web -o yaml
kubectl edit deployment synergychat-web



A Deployment is a higher-level abstraction that manages the ReplicaSets for you. You can think of a Deployment as a wrapper around a ReplicaSet.
kubectl get replicasets


First, download a copy of your deployment's YAML file and save it in your current directory:
kubectl get deployment synergychat-web -o yaml > web-deployment.yaml

To apply the changes, run:
kubectl apply -f web-deployment.yaml

You should get a warning that lets you know that you're missing the last-applied-configuration annotation. That's okay! we got that warning because we created this deployment the quick and dirty way, by using kubectl create deployment instead of creating a YAML file and using kubectl apply -f.

kubectl apply -f api-deployment.yaml

kubectl apply -f api-configmap.yaml
kubectl get configmaps

kubectl port-forward synergychat-api-cd5bdbb6d-h76fz 8080:8080

kubectl apply -f crawler-deployment.yaml
kubectl port-forward synergychat-crawler-f48dd646b-8sfhw 8080:8080

kubectl apply -f web-service.yaml
kubectl apply -f api-service.yaml
kubectl apply -f crawler-service.yaml
kubectl apply -f app-ingress.yaml

kubectl port-forward service/web-service 8080:80

kubectl get svc web-service -o yaml



minikube addons enable ingress



Open /etc/hosts in your favorite text editor and add the following lines:

127.0.0.1        synchat.internal
127.0.0.1        synchatapi.internal
ping synchat.internal

For WSL users, you also need to add the entries above to the Windows hosts file, C:\Windows\System32\drivers\etc\hosts. The WSL path to this file is /mnt/c/Windows/System32/drivers/etc/hosts. Run your terminal as an administrator from the Start menu.



Create a new ConfigMap for the web service. Add two new environment variables:
WEB_PORT: 8080 (this was already the default, now we're just making it explicit)
API_URL: http://synchatapi.internal
Update the web application's deployment to use the new ConfigMap.

kubectl apply -f web-configmap.yaml
kubectl apply -f web-deployment.yaml

kubectl logs <podname> --all-containers

kubectl get namespaces

kubectl create ns crawler
kubectl get ns
kubectl apply -f crawler-configmap.yaml
kubectl apply -f crawler-deployment.yaml
kubectl apply -f crawler-service.yaml

kubectl -n crawler get pods
kubectl -n crawler get svc
kubectl -n crawler get configmaps

kubectl delete deployment synergychat-crawler
kubectl delete service crawler-service
kubectl delete configmap synergychat-crawler-configmap



<service-name>.<namespace>.svc.cluster.local
http://crawler-service.crawler.svc.cluster.local:8080

 

 minikube addons enable metrics-server
 kubectl -n kube-system get pod
 kubectl top pod