- [Kubernetes](#kubernetes)
  * [Hello Kubernetes](#hello-kubernetes)
  * [Kubectl Command Cheat Sheet](#kubectl-command-cheat-sheet)
  * [Scaling Pod](#scaling-pod)
    + [Troubleshoot](#troubleshoot)
  * [Declarative Kubernetes](#declarative-kubernetes)
  * [Namespace](#namespace)
  * [Label](#label)
  * [Dashboard](#dashboard)
  * [Volume](#volume)
    + [Empty Dir](#empty-dir)
    + [Host Path](#host-path)
    + [Local](#local)
  * [Configmap](#configmap)
    + [Creating configmap from command line](#creating-configmap-from-command-line)
    + [Creating configmap from file](#creating-configmap-from-file)
  * [Secret](#secret)
    + [Creating secret from command line](#creating-secret-from-command-line)
    + [Creating secret from file](#creating-secret-from-file)
  * [Service](#service)
    + [NodePort](#nodeport)
- [Nginx Load Balancer](#nginx-load-balancer)
  * [Ingress Over TLS](#ingress-over-tls)
  * [Introducing Helm](#introducing-helm)
  * [Sealed Secret](#sealed-secret)
    + [Installing kubeseal](#installing-kubeseal)
    + [Syntax](#syntax)
  * [Resource Monitoring - Metrics Server](#resource-monitoring---metrics-server)
  * [Resource Monitoring - Kube Prometheus Stack](#resource-monitoring---kube-prometheus-stack)
  * [Monitoring Ingress Nginx](#monitoring-ingress-nginx)
  * [Ingress Nginx Combination](#ingress-nginx-combination)
    + [Official Documentation](#official-documentation)
  * [Autoscaling](#autoscaling)
  * [Stateful Set](#stateful-set)
  * [Stateful Set on Practice](#stateful-set-on-practice)
  * [Namespace Quota](#namespace-quota)
  * [Private Repository](#private-repository)
  * [Helm Spring Boot Rest API 01](#helm-spring-boot-rest-api-01)
  * [Helm Chartmuseum (Spring Boot REST API 02)](#helm-chartmuseum--spring-boot-rest-api-02-)
  * [Helm Spring Boot Rest API 03](#helm-spring-boot-rest-api-03)
  * [Helm Github As Repository (Spring Boot REST API 03)](#helm-github-as-repository--spring-boot-rest-api-03-)
  * [Multiple Helm Charts (Spring Boot REST API 04)](#multiple-helm-charts--spring-boot-rest-api-04-)
  * [ArgoCD](#argocd)
  * [Tips ArgoCD](#tips-argocd)
  * [ArgoCD Sensitive Data](#argocd-sensitive-data)
  * [Reloader](#reloader)
  * [CRD (Custom Resource Definition)](#crd--custom-resource-definition-)
  * [Cert Manager](#cert-manager)
- [East-West Traffic](#east-west-traffic)
  * [Istio : Kube-Prometheus](#istio---kube-prometheus)
- [Istio : Nginx](#istio---nginx)
  * [Istio : Basic](#istio---basic)
  * [Istio : Enable Sidecar Injection](#istio---enable-sidecar-injection)
  * [Istio : Kiali](#istio---kiali)
  * [Istio : Jaeger](#istio---jaeger)
  * [Istio : Open Telemetry](#istio---open-telemetry)
  * [Prometheus, Grafana, Istio](#prometheus--grafana--istio)
  * [Securing Kiali](#securing-kiali)
  * [Istio : Traffic Routing](#istio---traffic-routing)
  * [Istio : Nginx Combination](#istio---nginx-combination)
  * [Istio : Load Balancer](#istio---load-balancer)
  * [Istio : Canary Release](#istio---canary-release)
  * [Istio : Canary Release](#istio---canary-release-1)
  * [Istio : VirtualService](#istio---virtualservice)
  * [Istio : DestinationRule](#istio---destinationrule)
  * [Istio : Retry](#istio---retry)
  * [Istio : Circuit Breaker](#istio---circuit-breaker)
  * [Istio : Mutual TLS](#istio---mutual-tls)

---

# Kubernetes
## Hello Kubernetes

```bash
# Create deployment
kubectl create deployment my-nginx --image nginx:stable

# List deployments
kubectl get deployment
kubectl get deployments
kubectl get deploy

# Show documentation for 'get' command
kubectl get -h

# List pods 
kubectl get pod
kubectl get pods
kubectl get po

# Describe pod
kubectl describe pod [pod-name]

# Delete deployment (uncomment)
# kubectl delete deployment my-nginx

# Create service for nginx deployment
kubectl expose deployment my-nginx --type NodePort --port 80

# List service
kubectl get service
kubectl get services
kubectl get svc

# [Minikube only] Show exposed url
minikube service [service-name] --url
minikube service my-nginx --url
```
  
The `get` and `describe` can be used for many kubernetes resources.

```bash
# Get list of available kubernetes resources
kubectl api-resources

# Get list of object
kubectl get [resource-name-or-shortname]
kubectl get service
kubectl get svc
kubectl get deployment
kubectl get deploy

# Describe object
kubectl describe [resource-name-or-shortname] [object name]
kubectl describe service my-nginx
kubectl describe deployment my-nginx
kubectl describe pod my-nginx-someRandomNumber
```

## Kubectl Command Cheat Sheet
[Cheat sheet](https://kubernetes.io/docs/reference/kubectl/cheatsheet/)

## Creating deployment, Exposing and Scaling Pod - Demo

```bash
# Deploy your own image (from Dockerhub)
kubectl create deployment my-devops-blue --image [your-docker-username]/devops-blue:1.0.0

# Or you can use public image from my docker
kubectl create deployment my-devops-blue --image pndrns/devops-blue:1.0.0

# Create service for nginx deployment
kubectl expose deployment my-devops-blue --type LoadBalancer --port 8111 --name my-devops-blue-lb
minikube tunnel #Always keep it running on 2nd terminal

# Describe service
kubectl describe service my-devops-blue-lb

# Scale to 3 pod replicas
kubectl scale deployment my-devops-blue --replicas 3

# See detailed pod info
kubectl get pod -o wide
curl http://localhost:8111/devops/blue/api/hello #Traffic will go to different pods

```

- Endpoint to see the pod API docs : *http://localhost:8111/devops/blue/swagger-ui.html*
- Endpoint to see the pod IP : *http://localhost:8111/devops/blue/api/hello*
  

### Troubleshoot
If you cannot access *http://localhost:8111/...* after one minute, try this: 

  - **Docker Desktop** : 
     + click on the Docker whale icon on taskbar and restart docker desktop
     + or simply restart your laptop
  - **Minikube** :
     + open new terminal window, and run `minikube tunnel`
     + back to first terminal, and run `kubectl get service`, see the `EXTERNAL-IP` field for devops-blue service
     + curl to *http://[external-ip]:8111/...*


## Declarative Kubernetes - Demo

  - [Kubernetes YAML configuration reference (API reference)](https://kubernetes.io/docs/reference/kubernetes-api/)
  - [Common labels](https://kubernetes.io/docs/concepts/overview/working-with-objects/common-labels/)

```bash
https://github.com/pkumar-cloud/kubernetes-istio-tp/tree/main/kubernetes-istio-scripts/kubernetes/declarative-hello

# apply configuration
kubectl apply -f ./kubernetes-istio-scripts/kubernetes/declarative-hello
or
kubectl apply -f ./kubernetes-istio-scripts/kubernetes/declarative-single-file
kubectl get po --show-labels

# delete configuration
kubectl delete -f <path-to-config-file.yml>
```

----

## Namespace
**We will use `devops` as namespace. Please adjust the namespace (`-n`) parameter on `kubectl` into your own namespace if you use different namespace.**
  
----

## Label - Demo
- https://github.com/pkumar-cloud/kubernetes-istio-tp/tree/main/kubernetes-istio-scripts/kubernetes/label
- Ports used: 9011 to 9014
```bash
kubectl apply -f ./kubernetes-istio-scripts/kubernetes/label
curl http://localhost:9011-9014/devops/blue/api/hello #try with each service port
kubectl delete -f devops-label.yml
```
## Port forwarding - Demo
```bash
cd kubernetes-istio-scripts/kubernetes/port-forward
kubectl apply -f 
# get pod on namespace 'devops'
kubectl get pod -n devops
#kubectl port-forward [pod-name] [host-port]:[pod-port]
# forwarding one of the pod into host port 9111
# adjust the pod name
kubectl port-forward -n devops devops-port-forward-deployment-6d44c8c69d-kcvvj 9111:8111
curl http://localhost:9111/devops/blue/api/hello
```

## Health Check - Demo
```bash
cd kubernetes-istio-scripts/kubernetes/health-check
#All of the pods will be ready in at least 60 + 30 seconds
Postman -> health-check
kubectl delete -f 
```

## Log - Demo
```bash
cd kubernetes-istio-scripts/kubernetes/log
kubectl apply -f #or use the old deployment
Postman -> Log
#Display logs from all the pods
kubectl logs -n devops --selector app.kubernetes.io/name=devops-log-pod
```

## Dashboard

```bash
# Minikube addons list
minikube addons list

# K8s dashboard
minikube dashboard

# K8s Lens : Free & Open source.
Download: k8slens.dev or brew install --cask lens
```

## Volume - Demo
- cd kubernetes-istio-scripts/kubernetes/volume 
- Postman -> Volume (Upload image and check)
- curl http://localhost:9011/devops/blue/api/images
- curl http://localhost:9011/devops/blue/api/doc
### Empty Dir

```bash
# Apply the yml
kubectl apply -f devops-volume-empty-dir.yml

# Find the pod name on namespace 'devops'
kubectl get pod -n devops

# Find container name on certain pod (equals to pod spec on declarative file)
kubectl get pods -n devops [pod-name] -o jsonpath='{.spec.containers[*].name}'

# Run bash to devops-blue container inside [pod-name]
kubectl exec -n devops [pod-name] -c devops-blue -ti bash

# Run bin/sh (alpine linux shell) to alpine-linux container inside [pod-name]
kubectl exec -n devops [pod-name] -c alpine-linux -ti /bin/sh

#check for uploaded files under mount points from each POD. 

kubectl delete -f devops-volume-empty-dir.yml

```

### Host Path

```bash
kubectl apply -f devops-volume-host-path-minikube.yml
minikube tunnel
Postman: Volume -> upload image, upload doc and check
#Then list the image and the doc.
curl -v http://localhost:9011/devops/blue/api/images

# SSH into minikube to explore the file storage
minikube ssh
cd /data/upload/minikube #Path of voulume
ls

# Mount host directory to minikube, So we can see the uploaded files from host. adjust according your own machine. 
# Directory must exists
# Example (Windows): minikube mount d://minikube-volume:/data/upload/minikube
minikube mount [directory-on-host]:/data/upload/minikube
```

### Local volume

```bash

# Make sure minikube is stopped. Minikube does not support local volume.
# we will use docker desktop kubernetes.
#Enable k8s in docker desktop and create a folder on local disk.
# List nodes
kubectl get node

# Describe node (e.g. to see node's label)
kubectl describe node [node-name]

# Apply the yml
kubectl apply -f devops-volume-local-docker-desktop.yml
Postman: Volume -> upload image, upload doc and check
curl -v http://localhost:9011/devops/blue/api/images

# Find the pod name on namespace 'devops'
kubectl get pod -n devops

# Find container name on certain pod (equals to pod spec on declarative file)
kubectl get pods -n devops [pod-name] -o jsonpath='{.spec.containers[*].name}'

# Run bash to devops-blue container inside [pod-name]
kubectl exec -n devops [pod-name] -c devops-blue -ti bash

# Run bin/sh (alpine linux shell) to alpine-linux container inside [pod-name]
kubectl exec -n devops [pod-name] -c alpine-linux -ti /bin/sh

```

## Configmap

```bash
kubectl apply -f kubernetes-istio-scripts/kubernetes/configmap/devops-configmap.yml
Postman -> Configmap (copy the URL and launch in browser)

# List configmap
kubectl get configmap -n devops

# See configmap content (output as yaml)
kubectl get configmap -n devops -o yaml [configmap-name]

# See configmap content (output as json)
kubectl get configmap -n devops -o json [configmap-name]

# Edit configmap content & test color change in browser
kubectl edit configmap -n devops [configmap-name]

# Restart deployment (re-create pod without downtime)
kubectl rollout restart deployment -n devops [deployment-name]
```

### Creating configmap from command line

```bash
# Use --from-literal [key]=[value]
# Or  --from-literal=[key]=[value]
kubectl create configmap [configmap-name] -n devops --from-literal key.literal.one="This is my value for first key" --from-literal key.literal.two="While this is the value for second key"
```

### Creating configmap from file

The filename will become configmap key, while file content become configmap value.

```bash
# Basic syntax
kubectl create configmap [configmap-name] -n devops --from-file=[path-to-file]

# From single file, filename will become configmap key and file content become configmap value.
kubectl create configmap configmap-file-single -n devops --from-file=configmap-source.yml

# From multiple files sample (each filename will become one key and each file content as respective value.)
kubectl create configmap configmap-file-multi -n devops --from-file=configmap-source.json --from-file=configmap-source.properties --from-file=configmap-source.txt

# From binary (e.g. image), the content will be base64-encoded on configmap 
kubectl create configmap configmap-file-binary -n devops --from-file=configmap-source.png

# From folder (each filename will become one key)
kubectl create configmap configmap-folder -n devops --from-file=configmap-sources

```

## Secret
- cd /kubernetes-istio-scripts/kubernetes/secret
- Postman -> Secret

```bash
# List secret
kubectl get secret -n devops

# See secret content (output as yaml)
kubectl get secret -n devops -o yaml [secret-name]

# See secret content (output as json)
kubectl get secret -n devops -o json [secret-name]

# Edit configmap content
kubectl edit secret -n devops [secret-name]

# Restart deployment (re-create pod without downtime)
kubectl rollout restart deployment -n devops [secret-name]
```
### Creating secret from command line

```bash
# Use --from-literal [key]=[value]
# Or  --from-literal=[key]=[value]
kubectl create secret generic [secret-name] -n devops --from-literal key.literal.one="This is my secret value for first key" --from-literal key.literal.two="While this is the secret  value for second key" #kubernetes will automatically encode them as base64.
```

### Creating secret from file

The filename will become secret key, while file content become secret value.

```bash
# Basic syntax
kubectl create secret [secret-name] -n devops --from-file=[path-to-file]

# From single file. filename will become key and file content become value. kubernetes will automatically encode value as base64.
kubectl create secret secret-file-single -n devops --from-file=secret-source.yml

# From multiple files sample (each filename will become one key and each file content as respective value)
kubectl create secret secret-file-multi -n devops --from-file=secret-source.json --from-file=secret-source.properties --from-file=secret-source.txt

# From binary (e.g. image), the content will base64-encoded on configmap 
kubectl create secret secret-file-binary -n devops --from-file=secret-source.png

# From folder (each filename will become one key)
kubectl create secret secret-folder -n devops --from-file=secret-sources

```

## Service
- cd kubernetes-istio-scripts/kubernetes/service
- Postman: Service or use CURL to hit API: http://localhost:8111/devops/blue/api/hello

### Load balancer

```bash
kubectl apply -f deployment-service.yml
kubectl apply -f service-loadbalancer.yml
kubectl get svc -n devops
kubectl get svc -n devops -o wide devops-blue-loadbalancer
kubectl desc svc -n devops -o wide devops-blue-loadbalancer #will have two endpoints, which means this service will distribute traffic between two replicas.
kubectl delete -f service-loadbalancer.yml
```

### NodePort
```bash
kubectl apply -f service-nodeport.yml
# Expose nodeport on minikube. This will open random port
minikube service -n devops devops-blue-nodeport --url
use CURL or Postman
curl -v http://localhost:<fromAbove>/devops/blue/api/hello
kubectl delete -f service-nodeport.yml
```

### Cluster IP
```bash
kubectl apply -f service-clusterip.yml
# To hit the clusterip, create a kubectl proxy 
kubectl proxy --port=8888 # open proxy at port 8888
Postman -> ClusterIP
curl -> need to tell the proxy, which namespace we want to access,the service name, and the port name which we define in service configuration.
kubectl delete -f service-clusterip.yml

```

# Ingress - Nginx Load Balancer
[Nginx configuration](https://kubernetes.github.io/ingress-nginx/user-guide/nginx-configuration/)
[Ingress annotations](https://kubernetes.github.io/ingress-nginx/user-guide/nginx-configuration/annotations/)

```bash
- cd kubernetes-istio-scripts/kubernetes/ingress
- Postman -> Ingress - Nginx
# create blue pods
kubectl apply -f devops-ingress.yml

# create ingress
ingress-nginx-1..4.yml
kubectl apply -f ingress-nginx-1.yml
kubectl get ingress -n devops
kubectl desc ingress -n devops devops-ingress-nginx
minikube addons enable ingress #enable the ingress add on
minikube tunnel
kubectl get pod -n ingress-nginx #controller pod
kubectl get svc -n ingress-nginx
- Postman -> Ingress - Nginx
```
## Routing by Host
- Add entry to hosts file as per slide#63,64 for blue & yellow (1st part)
```bash
cd kubernetes-istio-scripts/kubernetes/ingress
kubectl apply -f ingress-nginx-3.yml
- Postman -> Ingress - Nginx -> Echo Blue/Yellow3
kubectl delete -f ingress-nginx-3.yml
```
- Add entry to hosts file as per slide#63,64 for blue & yellow (2nd part)
```bash
cd kubernetes-istio-scripts/kubernetes/ingress
kubectl apply -f ingress-nginx-4.yml
- Postman -> Ingress - Nginx -> Echo Blue/Yellow4
kubectl delete -f ingress-nginx-4.yml
kubectl delete -f devops-ingress.yml
```
## Ingress Over TLS

[Online self-signed SSL generator](https://regery.com/en/security/ssl-tools/self-signed-certificate-generator)
Or, use [OpenSSL](https://www.openssl.org/)
- Self-signed certificate is not trusted by most apps, so if we use Postman or browser, we need to disable validation later. To generate self-signed certificate, we can use openssl, or online tools.

```bash
Create self signed cert for "api.devops.local" using "regery.com" and download private key and crt file.
kubectl create secret tls api-devops-local-cert --key [path-to-private-key-file] --cert [path-to-crt-file] # Will create secret with type TLS, will be used in ingress-nginx-tls.yml
cd kubernetes-istio-scripts/kubernetes/ingress-tls
kubectl apply -f ..
Postman: Ingress - Nginx TLS # If you got SSL error, disable the verification, since we used free self signed certificate.
```

## Introducing Helm

- [Artifact hub](https://artifacthub.io/)
  - Install Jekins to test
- [Ingress Nginx](https://kubernetes.github.io/ingress-nginx/deploy/#quick-start)


```bash
#Install helm
helm version
helm repo list	
help repo add <Name> <RepoURL>
helm search <chartName>  #Searches in k8s helm repo. E.g. helm search jenkins
helm uninstall <ReleaseName>

#Install Jenkins from - [Artifact hub](https://artifacthub.io/)
helm repo add jenkinsci https://charts.jenkins.io/ #add the repo to local
helm repo list #Check repo exist
helm install my-jenkins jenkinsci/jenkins --version 5.1.5 #Install release “my-jenkins” with all the required resources 
kubectl get po
helm list --all-namespaces	 # List all the releases
#Follow the onscreen instructions and launch Jenkins 
helm uninstall my-jenkins

# Install nginx ingress controller using helm
# disable minikube addon first
minikube addons disable ingress
# Follow above Ingress Nginx link
helm upgrade --install ingress-nginx ingress-nginx --repo https://kubernetes.github.io/ingress-nginx --namespace ingress-nginx --create-namespace
minikube tunnel 
kubectl get po -n ingress-nginx
kubectl get svc -n ingress-nginx
```


## Sealed Secret

Full documentation [here](https://github.com/bitnami-labs/sealed-secrets).

### Installing kubeseal
[Non Windows](https://github.com/bitnami-labs/sealed-secrets#installation)
[Windows](https://github.com/bitnami-labs/sealed-secrets/releases). Find `kubeseal` release. In windows, it might not updated, so you might want to follow [this link](https://github.com/bitnami-labs/sealed-secrets#installation-from-source) for up-to-date release. However, installing from source requires [go installation](https://go.dev/dl/).
To extract tar.gz file on Windows, use tools like [7zip](https://www.7-zip.org/)

### Syntax
 ```bash
# Install sealed secret controller via helm on K8S cluster
helm upgrade --install sealed-secrets sealed-secrets --set-string fullnameOverride=sealed-secrets-controller --repo https://bitnami-labs.github.io/sealed-secrets --namespace kube-system

# Download kubeseal from above bitnami github link and go to dir
cd kubeseal
copy the ../sealed-secret/my-config-file.yml file here 

# Create secret, output it only at terminal (not upload to kubernetes cluster)
kubectl create secret generic my-secret -n devops -o yaml --dry-run=client --from-file [path to my-config-file.yml]  => Save it as my-secret-file.yml file

# Download public certificate from kubeseal controller 
kubectl get svc -n kube-system
kubeseal --controller-name=sealed-secrets-controller --controller-namespace=kube-system --fetch-cert > mycert.pem

# Seal the secret using kubeseal (using the public cert downloaded above)
kubeseal -flag < [path-to-input-secret-file] > [path-to-output-sealed-secret-file]
# Example, output is yml file format, using mycert.pem public certificate
kubeseal --cert mycert.pem -o yaml < my-secret-file.yml > my-sealed-secret-file.yml

# Apply the sealed secret
kubectl apply -f my-sealed-secret-file.yml #only way to read the secret using k8s secret now
kubectl get secret -n devops -o json my-secret # will get base64 encoded value. if we take the data, and decode it, we will get the plain text value

#Deploy the app
kubectl apply -f ../sealed-secret/devops-sealed-secret.yml
PostMan: Configmap / Secret -> Sealed secret #Launch the URL in browser to read the secrtet 

# Port forward the sealed secret service to port 8899
kubectl port-forward -n kube-system service/sealed-secrets-controller 8899:8080

 ```

## Resource Monitoring - Metrics Server

```bash
# Delete minikube cluster
minikube delete

# Restart using workaround to enable resource monitor
minikube start --extra-config=kubelet.housekeeping-interval=10s

# Enable metrics server
minikube addons enable metrics-server

# Enable ingress
minikube addons enable ingress
kubectl get pod -n ingress-nginx

# Tunneling
# Access via localhost/grafana    localhost/prometheus    localhost/alertmanager
minikube tunnel

cd kubernetes-istio-scripts/kubernetes/monitoring-metrics-server
kubectl apply -f devops-monitoring.yml

# There are two monitors available from metrics server.
kubectl top node
kubectl top pod -n devops

# use Postman to simulate fake load
Monitoring -> Fake load for 60 sec

kubectl delete -f devops-monitoring.yml #to start fresh next section
```
## Resource Monitoring - Kube Prometheus Stack 
- To see more visualized, historical resource usages, we can use Prometheus & Grafana.
**NOTE : Kube prometheus stack might not works on local kubernetes (like minikube)**

[Official documentation](https://github.com/prometheus-community/helm-charts/tree/main/charts/kube-prometheus-stack)

```bash
# Enable metrics server
minikube addons enable metrics-server

# Enable ingress or can use helm chart ingress controller
minikube addons enable ingress

# Installing kube-prometheus-stack via helm, with additional parameters. 
# Run this in folder monitoring-kube-prometheus
cd kubernetes-istio-scripts/kubernetes/monitoring-kube-prometheus
kubectl apply -f devops-monitoring.yml

helm upgrade --install my-kube-prometheus-stack --repo https://prometheus-community.github.io/helm-charts kube-prometheus-stack --namespace monitoring --create-namespace --values values-monitoring.yaml #using custom values.yaml file

kubectl get po -n monitoring

# Tunneling
# Access via localhost/grafana    localhost/prometheus    localhost/alertmanager
minikube tunnel

# use Postman to simulate fake load
Monitoring -> Fake load for 120 sec
#Play with built-n grafan dahsboards
# ----------------------------------------
# Alternative (expose via port-forward)
# Prometheus at localhost:9000
kubectl port-forward service/my-kube-prometheus-stack-prometheus -n monitoring 9090:9090

# Grafana at localhost:3000
kubectl port-forward service/my-kube-prometheus-stack-grafana -n monitoring 3000:80

# Alert manager at localhost:9093
kubectl port-forward service/my-kube-prometheus-stack-alertmanager -n monitoring 9093:9093

kubectl delete -f devops-monitoring.yml #to start fresh next section
```

## Monitoring Nginx (Ingress) via Prometheus
**NOTE : Kube prometheus stack might not works on local kubernetes (like minikube)**
```bash
# Fresh start - delete minikube cluster
minikube delete

# Fresh start - restart using workaround to enable resource monitor
minikube start --extra-config=kubelet.housekeeping-interval=10s

# 1. Disable minikube ingress
minikube addons disable ingress

# 2. Enable metrics server
minikube addons enable metrics-server

# 3. Install/upgrade nginx ingress controller using helm
cd kubernetes-istio-scripts/kubernetes/monitoring-ingress-nginx

helm upgrade --install ingress-nginx ingress-nginx --repo https://kubernetes.github.io/ingress-nginx --namespace ingress-nginx --create-namespace --values values-ingress-nginx.yaml #Configure Ingress using custom values.yaml file

# Optional : Check that nginx configured
helm get values ingress-nginx --namespace ingress-nginx

# 4. Install/upgrade kube-prometheus-stack using helm
helm upgrade --install my-kube-prometheus-stack --repo https://prometheus-community.github.io/helm-charts kube-prometheus-stack --namespace monitoring --create-namespace --values values-kube-prometheus.yml #re-configure the kube-prometheus stack using yaml configuration file
# Optional : Check that prometheus is configured
helm get values my-kube-prometheus-stack --namespace monitoring

#5,6 are Not required if #3 & #4 is done. 
# 5. Values-ingress-nginx is nginx configuration. We will re-configure the nginx installation using this configuration.
helm upgrade ingress-nginx ingress-nginx --repo https://kubernetes.github.io/ingress-nginx --namespace ingress-nginx --values values-ingress-nginx.yml
# 6. Upgrade kube-prometheus installation. Run on folder monitoring-ingress-nginx
#values-kube-prometheus yaml is for kube-prometheus-stack configuration. We will re-configure the prometheus using this configuration. This will create ingress rules for prometheus and grafana so we can see them without using port forward.
helm upgrade my-kube-prometheus-stack kube-prometheus-stack --repo https://prometheus-community.github.io/helm-charts --namespace monitoring --values values-kube-prometheus.yml

# 7. Apply the deployment sample
kubectl apply -f devops-monitoring.yml

# 8. Tunneling
# Access via localhost/grafana    localhost/prometheus/graph
minikube tunnel

Import prebuilt dashboards from [grafana community](https://grafana.com/grafana/dashboards/)
- NGINX Ingress controller #for the ingress controller status.
- Kubernetes Nginx Ingress Prometheus NextGen #to find traffic status.

Use Postman -> Ingress - Monitoring #Run the collection for many times. e.g. 10000 iterations @ 50 ms delay.
```

## Ingress Nginx Combination - Advance use cases

### Official Documentation
  - [Nginx ingress annotation](https://kubernetes.github.io/ingress-nginx/user-guide/nginx-configuration/annotations)
  - [Nginx ingress config map](https://kubernetes.github.io/ingress-nginx/user-guide/nginx-configuration/configmap)
  - [Nginx variables](http://nginx.org/en/docs/varindex.html)

```bash
# Fresh start - delete minikube cluster
minikube delete

# Fresh start - restart using workaround to enable resource monitor
minikube start --extra-config=kubelet.housekeeping-interval=10s

cd ingress-combination

# Generate K8s secret for TLS certificate. Follow from last exercise. 
kubectl create secret tls api-devops-local-cert -n devops --key [path-to-key-file] --cert [path-to-crt-file]

# 1. Install nginx ingress controller using helm
helm upgrade --install my-ingress-nginx ingress-nginx --repo https://kubernetes.github.io/ingress-nginx --namespace ingress-nginx --create-namespace

# 2. Apply the deployment sample
kubectl apply -f ingress-combination-deployment.yml

# 3. create the ingress, we separate blue and yellow ingress, despite both on same host. because we will have different configuration for each.
kubectl apply -f ingress-combination-ingress.yml

Use Postman -> Ingress - Combination #Run the Blue API (with 50 request, would fail as restrited to receive 30 requests) & Yellow API (will redirect to Goodle. Allow Automatic redirection in Postman settings). 

# 4. Tunneling
# Access via localhost/grafana    localhost/prometheus
minikube tunnel

# 5. Apply nginx config for adding custom request / response headers. To do this, we need to define configmap containing the headers.
kubectl apply -f ingress-combination-nginx-configmap-headers.yml
kubectl apply -f ingress-combination-nginx-configmap-config.yml # Confifure nginx controller using created request/response config maps. 

# 6. restart nginx controller to get the request/response headres added, as there is limitation in nginx that added headers will not be automatically effective.
kubectl get deploy -n ingress-nginx 
kubectl rollout restart deployment -n ingress-nginx my-ingress-nginx-controller #Will create new pod with updated configuration, and terminate old one.
kubectl get pod -n ingress-nginx
Use Postman -> Ingress - Combination #Run the Blue API, will see TWO new added request/response headres

#Delete later -------
# Monitoring - 1. Enable metrics server
minikube addons enable metrics-server

# Monitoring - 2. Install kube-prometheus-stack using helm
helm upgrade --install my-kube-prometheus-stack --repo https://prometheus-community.github.io/helm-charts kube-prometheus-stack --namespace monitoring --create-namespace

# Monitoring - 3. Upgrade nginx installation
helm upgrade my-ingress-nginx ingress-nginx --repo https://kubernetes.github.io/ingress-nginx --namespace ingress-nginx --values values-ingress-nginx.yml

# Monitoring - 4. Upgrade kube-prometheus installation
helm upgrade my-kube-prometheus-stack kube-prometheus-stack --repo https://prometheus-community.github.io/helm-charts --namespace monitoring --values values-kube-prometheus.yml
#Delete later -------
```

## Autoscaling

```bash
# Enable metrics server
minikube addons enable metrics-server

# Apply the deployment sample
cd kubernetes-istio-scripts/kubernetes/autoscaling
kubectl apply -f devops-autoscaling.yml

# Apply Horizontal Pod Autoscaler (HPA)
kubectl apply -f devops-hpa.yml

# Examine HPA (might take times for metric update)
kubectl get hpa -n devops

Postman -> Autoscaling #Run this endpoint with 1 CPU thread for 600 second.
```

## Stateful Set

```bash
cd kubernetes-istio-scripts/kubernetes/stateful-set
kubectl apply -f devops-deployment.yml
kubectl get po,pv,pvc -n devops #verify 
kubectl delete -f devops-deployment.yml

kubectl apply -f devops-stateful-set.yml
kubectl get po,pv,pvc -n devops #verify - only one pod will be created at a time.

# to access statefulset via ingress
kubectl apply -f devops-stateful-set-ingress.yml

Postman -> Stateful set

Alternate:
kubectl port-forward -n devops devops-stateful-set-main-0 9110:8111
kubectl port-forward -n devops devops-stateful-set-main-1 9111:8111
kubectl port-forward -n devops devops-stateful-set-main-2 9112:8111
```

## Stateful Set on Practice

```bash
kubectl apply -f devops-rabbitmq.yml #have secret, contains password which is required to config rabbitmq
#from artifacthub.io
add rabbitmq.local to /etc/hosts
helm upgrade --install my-rabbitmq rabbitmq --repo https://charts.bitnami.com/bitnami  --namespace rabbitmq --create-namespace --values values-rabbitmq.yml
```

The URL is **http://rabbitmq.local/rabbitmq/** (it has **/** at the end)
- Launch: rabbitmq.local/rabbitmq -> admin/password
- Create a dummy queue of type Quorum, to replicate data across rabbitmq instances
- publish 10 data to the queue, using publish message
- Access each POD using below ports e.g. http://localhost:9220/rabbitmq/ #can see have 10 messages each 
- change replica values-rabbitmq.yml and redploy release and verify data consistency

```bash
kubectl port-forward -n rabbitmq my-rabbitmq-0 9220:15672
kubectl port-forward -n rabbitmq my-rabbitmq-1 9221:15672
kubectl port-forward -n rabbitmq my-rabbitmq-2 9222:15672

helm upgrade --install my-rabbitmq rabbitmq --repo https://charts.bitnami.com/bitnami  --namespace rabbitmq --create-namespace --values values-rabbitmq.yml
```

## Resource Quota
```bash
minikube start # default starts with 2 cpu and 4 gb
cd kubernetes-istio-scripts/kubernetes/resources-request-limit
kubectl apply -f devops-blue-invalid-one.yml # would fail
kubectl apply -f devops-blue-invalid-two.yml # would be rejected
kubectl apply -f devops-blue-invalid-three.yml #error, where the volume is not bound.

```


## Namespace Quota
[ResourceQuota reference](https://kubernetes.io/docs/concepts/policy/resource-quotas/)
```bash
cd kubernetes-istio-scripts/kubernetes/namespace-quota
kubectl apply -f devops-resourcequota.yml #apply namespace quota
kubectl apply -f devops-deployment.yml #only 4 pods will come up
kubectl get resourcequota -n devops #see the resource limits of NS
kubectl describe resourcequota -n devops <ResourceQuotaName>
kubectl apply -f devops-pod.yml #error

```
## Service Account
```bash
cd kubernetes-istio-scripts/kubernetes/service-account

```
## security-context
```bash
cd kubernetes-istio-scripts/kubernetes/security-context
Postman -> Security Context
```

## Private Repository - Create own docker private repo

```bash
kubectl create ns devops
# Create secret for dockerhub. Unlike regular secret, docker credential requires specific type and data to be used.
kubectl create secret docker-registry dockerhub-secret --docker-server=https://index.docker.io/v1/ --docker-username=[your-username] --docker-password=[your-password] --docker-email=[your-email]
cd kubernetes-istio-scripts/kubernetes/private-repository
kubectl apply -f 
Postman -> Private Repository
```

## Helm Spring Boot Rest API 01

[Helm template tricks & tips](https://helm.sh/docs/howto/charts_tips_and_tricks/)
[Helm template best practice](https://helm.sh/docs/howto/charts_tips_and_tricks/)
[Go template language](https://pkg.go.dev/text/template)

```bash
# Run these on folder helm-charts

# [Optional] Render helm template
helm template helm-yellow-01 spring-boot-rest-api --namespace devops --create-namespace --values ..\kubernetes\helm-spring-boot-rest-api-01\values-spring-boot.yml

# Install helm release
helm upgrade --install helm-yellow-01 spring-boot-rest-api --namespace devops --create-namespace --values ..\kubernetes\helm-spring-boot-rest-api-01\values-spring-boot.yml

#list helm
helm list -n devops
Postman: Helm - Spring Boot REST API
helm uninstall -n devops helm-yellow-01
```


## Helm Chartmuseum (Spring Boot REST API 02)
 - [Artifact hub](https://artifacthub.io/packages/helm/chartmuseum/chartmuseum)
 - [Chartmuseum API List](https://github.com/helm/chartmuseum#api)

```bash
# Create chartmuseum username & password using secret
kubectl apply -f secret-chartmuseum.yml

# Install chartmuseum on helm
helm upgrade --install my-chartmuseum chartmuseum --repo https://chartmuseum.github.io/charts  --namespace chartmuseum --create-namespace --values values-chartmuseum.yml

# Package chart as tgz to be uploaded
helm package spring-boot-rest-api

# Create release spring-boot-rest-api from local chartmuseum, use chart version 0.1.0
helm upgrade --install helm-yellow-02 spring-boot-rest-api --repo http://chartmuseum.local/chartmuseum --username chartmuseum --password password --namespace devops --create-namespace --version 0.1.0 --values values-spring-boot.yml
```

## Helm Spring Boot Rest API 03

```bash
# Install helm release (values.yml + values-dev.yml)
helm upgrade --install helm-blue-03 spring-boot-rest-api --repo http://chartmuseum.local/chartmuseum --username chartmuseum --password password --namespace devops --create-namespace --version 0.1.0 --values values.yml --values values-dev.yml 

# Install helm release (values.yml + values-prod.yml)
helm upgrade --install helm-blue-03 spring-boot-rest-api --repo http://chartmuseum.local/chartmuseum --username chartmuseum --password password --namespace devops --create-namespace --version 0.1.0 --values values.yml --values values-prod.yml 
```


## Helm Github As Repository (Spring Boot REST API 03)

```bash
helm upgrade --install helm-blue-03-github spring-boot-rest-api --repo https://[username].github.io/[repository-name]/ --namespace devops --create-namespace --version 0.1.0 --values values.yml --values values-dev.yml 
```


## Multiple Helm Charts (Spring Boot REST API 04)

```bash
# Update chart dependencies (run on folder which contains Chart.yaml)
helm dependency update

# Run chart with dependencies (run on folder which contains values.yml and values-dev.yml)
helm upgrade --install helm-blue-04 chart-with-dependencies --namespace devops --create-namespace --values values.yml --values values-dev.yml 
```


## ArgoCD

```bash
# Install argocd
helm upgrade --install my-argocd argo-cd --repo https://argoproj.github.io/argo-helm --namespace argocd --create-namespace --values values-argocd.yml
```

[Create github personal access token](https://docs.github.com/en/authentication/keeping-your-account-and-data-secure/creating-a-personal-access-token)


## Tips ArgoCD

[Notification](https://argocd-notifications.readthedocs.io)
[User management](https://argo-cd.readthedocs.io/en/stable/operator-manual/user-management)


## ArgoCD Sensitive Data

The yaml references available on folder `kubernetes-istio-docs/devops-app-deployment`

```bash
kubeseal --controller-name=sealed-secrets-controller --controller-namespace=kube-system --fetch-cert > mycert.pem

# Example, output is yml file format, using mycert.pem public certificate
kubeseal --cert mycert.pem -o yaml < devops-secret-plain.yml > devops-secret-sealed.yml
```


## Reloader

[Documentation](https://github.com/stakater/Reloader)

```bash
# Install reloader
helm upgrade --install my-reloader reloader --repo https://stakater.github.io/stakater-charts --namespace reloader --create-namespace

# Sealed secret certificate
kubeseal --controller-name=sealed-secrets-controller --controller-namespace=kube-system --fetch-cert > mycert.pem

# Seal the secret
kubeseal --cert mycert.pem -o yaml < devops-reloader-secret-plain.yml > devops-reloader-secret-sealed.yml

# Apply the sealed secret
kubectl apply -f devops-reloader-secret-sealed.yml

# Apply configmap
kubectl apply -f devops-reloader-configmap.yml

# Apply deployment
kubectl apply -f devops-reloader-deployment.yml
```


## CRD (Custom Resource Definition)

[ArgoCD CRD](https://argo-cd.readthedocs.io/en/stable/operator-manual/declarative-setup/)


## Cert Manager
```bash
helm upgrade --install cert-manager cert-manager --repo https://charts.jetstack.io --namespace cert-manager --create-namespace --set installCRDs="true"
```


# East-West Traffic

[Blue API documentation](http://api.devops.local/devops/blue/swagger-ui.html)


## Istio : Kube-Prometheus 

```bash
# Install kube-prometheus
helm upgrade --install kube-prometheus-stack --repo https://prometheus-community.github.io/helm-charts kube-prometheus-stack --namespace istio-system --create-namespace --values values-kube-prometheus.yml
```


# Istio : Nginx

```bash
# nginx
helm upgrade --install ingress-nginx ingress-nginx --repo https://kubernetes.github.io/ingress-nginx --namespace ingress-nginx --create-namespace --values values-ingress-nginx.yml
```


## Istio : Basic
[Istio homepage](https://istio.io/)
[Istio Installation guide](https://istio.io/latest/docs/setup/platform-setup/)

```bash
# Istio base & CRD
helm upgrade --install istio-base base --repo https://istio-release.storage.googleapis.com/charts --namespace istio-system --create-namespace

# Istio daemon (istiod)
helm upgrade --install istiod istiod --repo https://istio-release.storage.googleapis.com/charts --namespace istio-system --create-namespace

# Scrape istio metrics
kubectl apply -f istio-scrape.yml

# Deployment apply
kubectl apply -f devops-istio-basic-deployment-1.0.0.yml
```


## Istio : Enable Sidecar Injection

```bash
# add label to namespace
kubectl apply -f devops-istio-enable-sidecar.yml

# dummy pod
kubectl apply -f devops-istio-dummy-blue.yml

# restart existing deployment
kubectl rollout restart deployment -n devops

# remove dummy pod
kubectl delete -f devops-istio-dummy-blue.yml
```


## Istio : Kiali
[Kiali homepage](https://kiali.io/)
[Kiali yml configuration](https://kiali.io/docs/configuration/kialis.kiali.io/)

```bash
# Kiali Server
helm upgrade --install kiali-server kiali-server --repo https://kiali.org/helm-charts --namespace istio-system --create-namespace --values values-kiali-server.yml
```


## Istio : Jaeger

```bash
# Need to install certmanager
helm upgrade --install cert-manager cert-manager --repo https://charts.jetstack.io --namespace cert-manager --create-namespace --set installCRDs="true"

# create namespace
kubectl apply -f devops-istio-jaeger-namespace.yml

# Create jaeger operator
# Latest version, google with keyword : jaegertracing.io install kubernetes operator
# wait for operator to complete
kubectl apply -f https://github.com/jaegertracing/jaeger-operator/releases/download/v1.36.0/jaeger-operator.yaml -n observability

# Install simple jaeger
kubectl apply -f install-jaeger-simple.yml

# Update istiod, send 100% trace to jaeger
helm upgrade istiod istiod --repo https://istio-release.storage.googleapis.com/charts --namespace istio-system --values values-istiod-after-jaeger.yml

# apply deployment
kubectl apply -f devops-istio-basic-deployment-1.0.1.yml
```


## Istio : Open Telemetry

```bash
# Make sure cert-manager already installed
helm upgrade --install cert-manager cert-manager --repo https://charts.jetstack.io --namespace cert-manager --create-namespace --set installCRDs="true"

# Install open telemetry operator
kubectl apply -f https://github.com/open-telemetry/opentelemetry-operator/releases/latest/download/opentelemetry-operator.yaml

# Install open telemetry objects
kubectl apply -f devops-open-telemetry.yml

# Deploy application with open telemetry sidecar
kubectl apply -f devops-istio-basic-deployment-1.0.0-otel.yml
```

## Prometheus, Grafana, Istio

```bash
# Port forward application
kubectl port-forward -n devops [istio-otel-deployment-blue-pod-name] 8111:8111

# Port forward istio
kubectl port-forward -n devops [istio-otel-deployment-blue-pod-name] 15020:15020
```

URL for prometheus:
 - application : http://localhost:8111/devops/blue/actuator/prometheus
 - istio : http://localhost:15020/stats/prometheus

## Securing Kiali

```bash
# Create service account token
kubectl apply -f kiali-sa-token.yml

# Use token auth
helm upgrade --install kiali-server kiali-server --repo https://kiali.org/helm-charts --namespace istio-system --create-namespace --values values-kiali-server-secure.yml
```

## Istio : Traffic Routing
Clear all deployment (pod, service, ingress) on `devops` namespace, then apply below 

```bash
# 1.0.0
kubectl apply -f devops-istio-routing-1.0.0.yml

# 1.0.1 (additional pod)
kubectl apply -f devops-istio-routing-1.0.1.yml

# Service & Ingress
kubectl apply -f devops-istio-routing-service.yml
```


## Istio : Nginx Combination
 - Clear all deployment (pod, service, ingress) on `devops` namespace, then apply below.
 - Clear all istio objects (virtual service, destination rule)

```bash
# sidecar injection
kubectl apply -f devops-istio-nginx-namespace.yml

# restart ingress
kubectl rollout restart deployment -n ingress-nginx

# Deployment & Service
kubectl apply -f devops-istio-nginx.yml

# Ingress
kubectl apply -f devops-istio-nginx-ingress.yml
```


## Istio : Load Balancer

[Istio Destination Rule](https://istio.io/latest/docs/reference/config/networking/destination-rule/)

 - Clear all deployment (pod, service, ingress) on `devops` namespace, then apply below.
 - Clear all istio objects (virtual service, destination rule)

```bash
kubectl apply -f devops-istio-load-balancer.yml
```


## Istio : Canary Release
 - Clear all deployment (pod, service, ingress) on `devops` namespace, then apply below.
 - Clear all istio objects (virtual service, destination rule)

```bash
# 1.0.0
kubectl apply -f devops-istio-canary-1.0.0.yml

# 1.0.1 (new white pod)
kubectl apply -f devops-istio-canary-1.0.1.yml

# Service & Ingress
kubectl apply -f devops-istio-canary-service.yml
```


## Istio : Canary Release
 - Clear all deployment (pod, service, ingress) on `devops` namespace, then apply below.
 - Clear all istio objects (virtual service, destination rule)

```bash
kubectl apply -f devops-istio-dark-launching.yml
```


## Istio : VirtualService
[Istio VirtualService official documentation](https://istio.io/latest/docs/reference/config/networking/virtual-service)


## Istio : DestinationRule
[Istio DestinationRule official documentation](https://istio.io/latest/docs/reference/config/networking/destination-rule)


## Istio : Retry
Use this on `VirtualService.spec.retryOn` : `5xx,400,401,403,404,405,406,408,413,415,429,431` 


## Istio : Circuit Breaker


## Istio : Mutual TLS 

```bash
# Modify namespace (enable / disable sidecar) & apply the file
kubectl apply -f devops-istio-mtls-deployment.yml

# Modify tls mode and apply
kubectl apply -f devops-istio-mtls-mode.yml

# Reload deployment on namespaces after namespace modification
kubectl rollout restart deployment -n ingress-nginx

kubectl rollout restart deployment -n devops-blue

kubectl rollout restart deployment -n devops-yellow

kubectl rollout restart deployment -n devops-white
```


kubectl apply -f devops-istio-mtls-deployment.yml

kubectl apply -f devops-istio-mtls-mode.yml

kubectl rollout restart deployment -n ingress-nginx

kubectl rollout restart deployment -n devops-blue

kubectl rollout restart deployment -n devops-yellow

kubectl rollout restart deployment -n devops-white
