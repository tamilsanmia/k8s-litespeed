# k8s-litespeed



## K8s Cluster Infomation
```
Node 1
Name Tools : tools
Spce : Basic - AMD - 2vCPU 8 GB RAM
Cost : $42.00


- Metrics-server 
- Ingress
- cert-manager
- Prometheus Grafana 
- harbor 
- argocd
```

```
Node 2
Name Tools : faveo 
Spce : Basic - AMD - 4vCPU 8 GB RAM
Cost : $56.00

- faveo-app
- faveo-supervisor
```

```
Node 3
Name Tools : cron-jobs 
Spce : Basic - 1vCPU 2 GB RAM
Code : $12.00

- cron-job
```
```
Total cost - $110/month
```

## Configuring and Deploying Faveo Helpdesk

This manifest will contain all of the configuration details needed to deploy your Faveo app to your cluster.

```
kubectl create namespace faveo
```
Apply the Cluster Issuer resource
```
kubectl apply -f deploy-faveo.yaml
```

## 1. Install Metrics Server

### 1. Add the Metrics Server Helm repository:
```
helm repo add metrics-server https://kubernetes-sigs.github.io/metrics-server/
helm repo update
```

### 2. Create a namespace
```
kubectl create namespace metrics-server
```

### 3. Install the Metrics Server with the custom values.yaml file:

```
helm install metrics-server metrics-server/metrics-server -n  metrics-server 
```

### 4. Verify the installation:
```
kubectl get deployment metrics-server -n metrics-server
```


## 2. Install LS Ingress 


### 1. Create a namespace
```
kubectl create namespace ls-k8s-webadc
```

### 2. Adding a Licence

```
kubectl create secret generic -n ls-k8s-webadc ls-k8s-webadc --from-file=trial=./trial.key
```

### 3. Add the Litespeed Ingress Helm repository

```
helm repo add ls-k8s-webadc https://litespeedtech.github.io/helm-chart/
helm repo update
```

### 4. Install the Litespeed Ingress
```
helm install ls-k8s-webadc ls-k8s-webadc/ls-k8s-webadc -n ls-k8s-webadc 
```

### 5. Verify the installation:
```
kubectl get --namespace ls-k8s-webadc svc -w ls-k8s-webadc
```

## 2. Cert Manager

### 1. Add the Helm repository for cert-manager:

```
helm repo add jetstack https://charts.jetstack.io
helm repo update
```

### 2. Install cert-manager with Helm and CRDs:

```
helm install cert-manager jetstack/cert-manager \
  --namespace cert-manager \
  --create-namespace \
  --version v1.10.0 \
  --set installCRDs=true
```

### 3. Verify the Installation
```
kubectl get pods -n cert-manager -o wide
```

### 4. Create an Secret resource for Cloudflare DNS

creating a file named `cf-secret.yaml`

Apply the Secret resource

```
kubectl apply -f cf-secret.yaml
```

### 5. Create an Cluster Issuer resource for Cert manager

creating a file named `cluster-issuer.yaml`

Apply the Cluster Issuer resource
```
kubectl apply -f cluster-issuer.yaml
```


## 3. Install Prometheus Operator and Grafana 

### 1. Create a new namespace for Prometheus Operator 

Create a new namespace called `monitoring`:
```
kubectl create namespace monitoring
```

### 2. Add the Prometheus Operator Helm repository

Add the Prometheus community Helm repository and update it:
```
helm repo add prometheus-community https://prometheus-community.github.io/helm-charts
helm repo update
```

### 3. Install the Prometheus Operator chart from the prometheus-community repository:

Install the Prometheus Operator chart from the prometheus-community repository into the monitoring namespace:

```
helm install prometheus prometheus-community/kube-prometheus-stack --namespace monitoring --create-namespace
```

### 4. Configure Nginx Ingress for Prometheus (optional)

Create an Ingress resource for Prometheus by creating a file named `prometheus-ingress.yaml`
Apply the Ingress resource
```
kubectl apply -f prometheus-ingress.yml
```

### 5. Configure Nginx Ingress for Grafana

Create an Ingress resource for Grafana by creating a file named `grafana-ingress.yml` 

Apply the Ingress resource
```
kubectl apply -f grafana-ingress.yml
```
### 6. Obtain Grafana Admin Password

Retrieve the Grafana admin password:

```
kubectl get secret --namespace monitoring prometheus-grafana -o jsonpath="{.data.admin-password}" | base64 --decode ; echo
```
### 7. Verify TLS Certificate Issuance

Verify that the TLS certificates for Prometheus and Grafana are issued correctly:

```
kubectl get certificates -n monitoring
kubectl describe certificate prometheus-tls -n monitoring
kubectl describe certificate grafana-tls -n monitoring
```
These steps should help you set up Prometheus and Grafana with Ingress and TLS certificates in your Kubernetes cluster.


## 4. Install Harbor

### 1. Create a values.yaml file for Harbor:

```
expose:
  type: ingress
  controller: true
  ingress:
    hosts:
      core: harbor.thetamilselvan.in
    annotations:
      kubernetes.io/ingress.class: lslbd
      cert-manager.io/cluster-issuer: "letsencrypt-prod"
    tls:
      - secretName: harbor-tls
        hosts:
          - '*.thetamilselvan.in'
harborAdminPassword: "Harbor12345"
externalURL: https://harbor.thetamilselvan.in
```

Notes:
- Make sure to replace **your_domain_name** with your actual domain name.
- The Harbor Admin Password is set to **"Harbor12345"** - consider changing it for security.

### 2. Download Harbor helm chart:

```
helm repo add harbor https://helm.goharbor.io
helm repo update
```

### 3. Create namespace

```
kubectl create namespace harbor
```

### 4. Install the Harbor helm chart:

```
helm install harbor harbor/harbor \
  --namespace harbor \
  -f values.yaml
```

### 5. Verify SSL Configuration:

```
kubectl get certificates -n harbor
```
Ensure that the certificate is in the "Ready" state.

