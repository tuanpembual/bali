# Slide

- Cover dan judul
Reliable Deployments with Kubernetes and Istio
Implement blue/green deployments and canary roll outs. 
- Perkenalan (copy from)
- Perkenalan Kubernetes
- Perkenalan Istio
- Blue Green deployment
- Canary deployment
- Gambar gif bagaimana proses ini terjadi
- Praktek | buat gist
- Mesin VM 1. 2core2gb
	* install docker, tanpa sudo
	* install kubectl
	* install minikube
	* install istio
	* buat delpoyment
	* buat rilis baru
	* setup load balancer
	* all deployed
- QA

## Prepare Tools
```
curl -LO https://storage.googleapis.com/kubernetes-release/release/$(curl -s https://storage.googleapis.com/kubernetes-release/release/stable.txt)/bin/linux/amd64/kubectl
chmod +x ./kubectl
sudo mv ./kubectl /usr/local/bin/kubectl
curl -Lo minikube https://storage.googleapis.com/minikube/releases/latest/minikube-linux-amd64 && chmod +x minikube && sudo mv minikube /usr/local/bin/
sudo apt-get install apt-transport-https ca-certificates curl gnupg-agent software-properties-common
curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo apt-key add -
sudo add-apt-repository "deb [arch=amd64] https://download.docker.com/linux/ubuntu $(lsb_release -cs) stable"
sudo apt-get install docker-ce docker-ce-cli containerd.io
sudo groupadd docker
sudo usermod -aG docker $USER
minikube version
minikube start --vm-driver=none
minikube status
```

```
kubectl run hello-minikube --image=gcr.io/google_containers/echoserver:1.4 --port=8080
kubectl expose deployment hello-minikube --type=NodePort
kubectl get services
kubectl delete services hello-minikube
kubectl delete deployment hello-minikube
minikube stop
minikube start --vm-driver=none
```

## Start
```
curl -L https://git.io/getLatestIstio | ISTIO_VERSION=1.3.0 sh -
cd istio-1.3.0
for i in install/kubernetes/helm/istio-init/files/crd*yaml; do kubectl apply -f $i; done
vim install/kubernetes/istio-demo.yaml
```
line 17647
```
apiVersion: v1
kind: Service
metadata:
  name: istio-ingressgateway
  namespace: istio-system
  annotations:
  labels:
    chart: gateways
    heritage: Tiller
    release: istio
    app: istio-ingressgateway
    istio: ingressgateway
spec:
```
save n exit

```
kubectl apply -f install/kubernetes/istio-demo.yaml
```

## Testing deploy 2 version apps
```
vim my-app.yaml
kubectl apply -f my-app.yaml
kubectl get pods
kubectl get svc
```

listen apps
```
kubectl port-forward --address 0.0.0.0 deployment/myapp-v1 8080:80
kubectl port-forward --address 0.0.0.0 deployment/myapp-v2 8081:80
```

create gateway
```
kubectl apply -f app-gateway.yaml
```

Testing load
```
vim app-gateway.yaml
kubectl replace -f app-gateway.yaml
```

setup ingres
```
kubectl -n istio-system get svc istio-ingressgateway -o jsonpath='{.spec.ports[?(@.name=="http2")].nodePort}'
```

## Testing

ip addr # get local ipaddress
```
while : ;do export GREP_COLOR='1;33';curl -s  172.31.25.31:31380 \
 |  grep --color=always "V1" ; export GREP_COLOR='1;36';\
 curl -s  172.31.25.31:31380 \
 | grep --color=always "vNext" ; sleep 1; done
```



## How to deploy Green and Blue Istio on K8s
### Realible Deployments with Kubernetes and Istio

When deployment or release of product. DevOps team have responsible to make no down time on process of deployment. For now, we have many ways and many tools to make it happen.

One of them is Blue Green Deployment using istio. At this time, I want presentation how implement Istio on Kubernetes Cluster.

Istio is a service mesh designed to make communication among microservices reliable, transparent, and secure. Istio intercepts the external and internal traffic targeting the services deployed in container platforms such as Kubernetes.

Though Istio is capable of many things including secure service-to-service communication, automated logging of metrics, enforcing a policy for access controls, rate limits, and quotas, we will focus exclusively on the traffic management features.

Istio lets DevOps teams create rules to intelligently route the traffic to internal services. It is extremely simple to configure service-level properties like circuit breakers, timeouts, and retries, to set up a variety of deployment patterns including blue/green deployments and canary roll outs.

The objective of this workshop is to help audience understand how to configure blue/green deployment of microservices running in Kubernetes with Istio.

Audience don’t need to have any prerequisites to explore this scenario except a basic idea of deploying pods and services in Kubernetes. We will configure everything from Minikube to Istio to the sample application.

There are four steps; Installing Minikube, Installing and verifying Istio, deploying two versions of the same app, and finally configuring the services for blue/green deployments. We will use two simple, pre-built container images that represent blue (V1) and green (V2) releases.

### FROM 
* https://www.radishlogic.com/kubernetes/running-minikube-in-aws-ec2-ubuntu/
* https://thenewstack.io/tutorial-blue-green-deployments-with-kubernetes-and-istio/
* https://cheatsheet.dennyzhang.com/cheatsheet-minikube-a4
