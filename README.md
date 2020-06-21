# 1. Install Docker

## Windows

Install Docker Desktop from https://www.docker.com/products/docker-desktop

## Mac

Install Docker Desktop from https://www.docker.com/products/docker-desktop

## Linux

Install Docker from your package manager following https://docs.docker.com/engine/install/ubuntu/

# 2. Install Kubectl


## Windows

Download https://storage.googleapis.com/kubernetes-release/release/v1.18.0/bin/windows/amd64/kubectl.exe

Add the binary in to your PATH.

```
kubectl version --client
```

## Mac

```
brew install kubectl 
kubectl version --client
```

## Linux

````
sudo apt-get update && sudo apt-get install -y apt-transport-https gnupg2
curl -s https://packages.cloud.google.com/apt/doc/apt-key.gpg | sudo apt-key add -
echo "deb https://apt.kubernetes.io/ kubernetes-xenial main" | sudo tee -a /etc/apt/sources.list.d/kubernetes.list
sudo apt-get update
sudo apt-get install -y kubectl
````

# 3. Install minikube for linux (optional for windows and mac)

```
curl -Lo minikube https://storage.googleapis.com/minikube/releases/latest/minikube-linux-amd64 \
  && chmod +x minikube
sudo mkdir -p /usr/local/bin/
sudo install minikube /usr/local/bin/
minikube start --driver=none
minikube status
```


# 4. Docker Hello world

```
docker run -d -p 80:80 tutum/hello-world
docker ps
```

Hit http://localhost:8080

Kill the container 

```
docker stop <container name>
```


# 5. Kubernetes Hello World

create deployment API object
```
kubectl create deployment hello-k8s --image=tutum/hello-world
```
get info from k8s API
```
kubectl get deployments
kubectl get pods
kubectl get events
```

expose deployment via service
```
kubectl expose deployment hello-k8s --type=LoadBalancer --port=80
kubectl get services
curl localhost:80
```

Update image

```
kubectl set image deployment/hello-k8s hello-world=nginxdemos/hello
rollout history deploy/hello-k8s

curl localhost:80

dojo-k8s kubectl rollout undo deploy/hello-k8s
```

Manually scale up instances

```
kubectl scale --replicas=3 deploy/hello-k8s
kubectl describe pods
curl localhost:80

```

cleanup 
```
kubectl delete service hello-k8s
kubectl delete deployment hello-k8s
```

#  6. Explore API Objects

https://kubernetes.io/docs/concepts/overview/working-with-objects/kubernetes-objects/

```
kubectl apply -f 6_api-objects/hello-k8s-deployment.yaml
kubectl apply -f 6_api-objects/hello-k8s-svc.yaml
```

inspect
```
kubectl get pods
kubectl get svc
kubectl get svc hello-k8s-service -o yaml
curl localhost:8080
```

clean up

```
kubectl delete deploy hello-k8s-deployment
kubectl delete svc hello-k8s-service
```

# 7. Install helm

## Windows

download from https://get.helm.sh/helm-v3.2.4-windows-amd64.zip and add to path

## Mac

```
curl -fsSL -o get_helm.sh https://raw.githubusercontent.com/helm/helm/master/scripts/get-helm-3
chmod 700 get_helm.sh
./get_helm.sh
```

## Linux

```
curl -fsSL -o get_helm.sh https://raw.githubusercontent.com/helm/helm/master/scripts/get-helm-3
chmod 700 get_helm.sh
./get_helm.sh
```

# 8. Better Ops with Helm

```
helm create hello-helm
```

update hello-helm/values.yaml, replace ClusterIp with LoadBalancer

```
helm install hello-helm hello-helm
helm history hello-helm
```

Mess around with any settings in the chart then upgrade

``` 
helm upgrade hello-helm hello-helm
helm history hello-helm
```

rollback
```
helm rollback hello-helm
```

cleanup 

```
helm delete hello-helm
```

# 9. K8s dashboard

Setup role bindings
```
kubectl apply -f 9-misc/dashboard-adminuser.yml
kubectl apply -f 9-misc/admin-role-binding.yml
```
install kubernetes-dashboard via helm
```
helm repo add kubernetes-dashboard https://kubernetes.github.io/dashboard/
helm install k8s-dashboard kubernetes-dashboard/kubernetes-dashboard --set service.type=LoadBalancer
```
browse to https://localhost

Get admin token

```
kubectl -n kube-system describe secret $(kubectl -n kube-system get secret | grep admin-user | awk '{print $1}')
```


Add the following to the deployment of k8s-dashboard-kubernetes-dashboard in the dashboard :D
```
- --enable-skip-login
- --disable-settings-authorizer  
```
