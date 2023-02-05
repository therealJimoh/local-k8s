# Deploying A Web App on Local K8s Cluster KinD and Access From Outside the Cluster with TLS


### Provision KinD cluster
```
kind create cluster --config kube.config
```

## Install Cert manager
```
kubectl apply -f ./cert-manager/cert-manager-v1.11.0.yaml
```

## Install nginx ingress controller
```
kubectl apply --filename https://raw.githubusercontent.com/kubernetes/ingress-nginx/master/deploy/static/provider/kind/deploy.yaml
```

## Create Cluster Issuer for Let's encrypt
```
kubectl apply -f ./cert-manager/cert-issuer.yaml
```

## Confirm the ACME account was registered with ACME server
``` 
kubectl describe clusterissuer letsencrypt-cluster-issuer
```

## Deploy your application
```
kubectl apply -f ./app/web.yml

kubectl apply -f ./app/web-svc.yml
```

## Creating a certificate object 
When creating/deploying a certificate object, there are two ways to go about it, both ways will be discussed but just one will be used in the project.
1. Deploying a certificate Kind
2. Appending cert-manager issuer annotation on Ingress

### Deploying a certificare Kind
```
kubectl apply -f certificate.yaml
```

#### Confirm the certificate has been issued
```
kubectl describe certificate dogbreed-app
```

#### Confirm a new serets has been created for the certification
```
kubectl get secrets
```

### Appending cert-manager issuer annotation on Ingress
Edit the ingress.yml file and append this annotations under metadata
```
annotations:
    cert-manager.io/issuer: "letsencrypt-cluster-issuer-key"
```
This line will go ahead to create the certification object and its secret.

## Expose your application to the Internet via the ingress
```
kubectl apply -f ingress.yml
```
You can now access the app via: app-127-0-0-1.nip.io


# Continuous Deployment Best Practices



