# Readme

This repo contain my slide and material for my workshop at openSUSE Asia Summit 2019, Udayana University 4-6 Oct 2019

## Prepare Tools
```install_kubectl_docker_minikube
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

```testing_kubectl
kubectl run hello-minikube --image=gcr.io/google_containers/echoserver:1.4 --port=8080
kubectl expose deployment hello-minikube --type=NodePort
kubectl get services
kubectl delete services hello-minikube
kubectl delete deployment hello-minikube
minikube stop
minikube start --vm-driver=none
```

## Start
```install_istio
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
vim app-gateway.yaml
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

## Load Testing Apps

ip addr # get local ipaddress
```
while : ;do export GREP_COLOR='1;33';curl -s  172.31.25.31:31380 \
 |  grep --color=always "V1" ; export GREP_COLOR='1;36';\
 curl -s  172.31.25.31:31380 \
 | grep --color=always "vNext" ; sleep 1; done
```
