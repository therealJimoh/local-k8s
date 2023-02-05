## Deploying A Web App on Local K8s Cluster KinD and Access From Outside the Cluster with TLS

# Provision Kind cluster
kind create cluster --config kube.config


# Install Cert manager
kubectl apply -f cert-manager-v1.11.0.yaml


# Install nginx ingress controller
kubectl apply --filename https://raw.githubusercontent.com/kubernetes/ingress-nginx/master/deploy/static/provider/kind/deploy.yaml

# Create Cluster Issuer for Let's encrypt
kubectl apply -f cert-issuer.yaml

# Confirm the ACME account was registered with ACME server 
kubectl describe clusterissuer letsencrypt-cluster-issuer

# Deploy your application
kubectl apply -f web.yml

# Deploy a certificate object for cert manager or append annotation of cert issuer on Ingress kind
kubectl apply -f certificate.yaml

# Confirm the certificate has been issued
kubectl describe certificate dogbreed-app

# Confirm a new serets has been created for the certification
kubectl get secrets

# Expose your application to the Internet via the ingress
kubectl apply -f ingress.yml



### Continuous Deployment Best Practices



