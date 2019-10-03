# Realible Deployments with Kubernetes and Istio
## How to deploy Green and Blue Istio on K8s

When deployment or release of product. DevOps team have responsible to make no down time on process of deployment. For now, we have many ways and many tools to make it happen.

One of them is Blue Green Deployment using istio. At this time, I want presentation how implement Istio on Kubernetes Cluster.

Istio is a service mesh designed to make communication among microservices reliable, transparent, and secure. Istio intercepts the external and internal traffic targeting the services deployed in container platforms such as Kubernetes.

Though Istio is capable of many things including secure service-to-service communication, automated logging of metrics, enforcing a policy for access controls, rate limits, and quotas, we will focus exclusively on the traffic management features.

Istio lets DevOps teams create rules to intelligently route the traffic to internal services. It is extremely simple to configure service-level properties like circuit breakers, timeouts, and retries, to set up a variety of deployment patterns including blue/green deployments and canary roll outs.

The objective of this workshop is to help audience understand how to configure blue/green deployment of microservices running in Kubernetes with Istio.

Audience don’t need to have any prerequisites to explore this scenario except a basic idea of deploying pods and services in Kubernetes. We will configure everything from Minikube to Istio to the sample application.

There are four steps; Installing Minikube, Installing and verifying Istio, deploying two versions of the same app, and finally configuring the services for blue/green deployments. We will use two simple, pre-built container images that represent blue (V1) and green (V2) releases.

## FROM 
* https://www.radishlogic.com/kubernetes/running-minikube-in-aws-ec2-ubuntu/
* https://thenewstack.io/tutorial-blue-green-deployments-with-kubernetes-and-istio/
* https://cheatsheet.dennyzhang.com/cheatsheet-minikube-a4
