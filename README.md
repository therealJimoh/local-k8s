# Setting Up A Web App on Local K8s Cluster KinD and Access From Outside the Cluster with TLS


### Provision KinD cluster
```
kind create cluster --config kube.config
```

### Install Cert manager
```
kubectl apply -f ./cert-manager/cert-manager-v1.11.0.yaml
```

### Install nginx ingress controller
```
kubectl apply --filename https://raw.githubusercontent.com/kubernetes/ingress-nginx/master/deploy/static/provider/kind/deploy.yaml
```

### Create Cluster Issuer for Let's encrypt
```
kubectl apply -f ./cert-manager/cert-issuer.yaml
```

### Confirm the ACME account was registered with ACME server
``` 
kubectl describe clusterissuer letsencrypt-cluster-issuer
```

### Deploy your application
```
kubectl apply -f ./app/web.yml

kubectl apply -f ./app/web-svc.yml
```

### Creating a certificate object 
When creating/deploying a certificate object, there are two ways to go about it, both ways will be discussed but just one will be used in the project.
1. Deploying a certificate Kind
2. Appending cert-manager issuer annotation on Ingress

#### Deploying a certificare Kind
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

### Expose your application to the Internet via the ingress
```
kubectl apply -f ingress.yml
```
You can now access the app via: app-127-0-0-1.nip.io


# Continuous Deployment Best Practices

### *Describe your approach to set up a CD solution for a Kubernetes environment, where there is a need to serve five teams of developers that can’t have direct kubectl access to the cluster and maintain their autonomy to deploy and see the production environment for debugging purposes.*
### 1. *How would you manage secrets inside?*  
### 2. *What tooling would you use?*


It is crucial to minimize potential risks, such as configuration drift, security and compliance risk, resource management, and difficulty in rollback, that come with developers or software engineers having direct access to a Kubernetes cluster in dev, staging, or production environments. This is not considered the best practice and could potentially harm the company's infrastructure. To mitigate these risks, it is important to utilize various tools and techniques. And the ones I would use are:

- CI/CD: Continuous Integration and Deployment (CI/CD) is a crucial aspect of DevOps that integrates with Kubernetes or cloud-based environments. The kube config file, which defines the cluster's configuration, can reside within a CI pipeline, such as Jenkins, Azure Pipelines, CircleCI, Github Actions, or GitLab CI. The CI pipeline retrieves the code from a Git repository, builds it, and runs tests (such as End-to-End or Integration tests) as required. If the tests pass, the deployment process is triggered based on the previously configured deployment strategy.
- Git: Implementing a git workflow through platforms such as Github, Gitlab, or Azure Repo ensures effective management of source control and version releases of a deployed application. The use of git workflow and continuous deployment (CD) makes it possible to revert to previous versions quickly in case of any issues that arise in the production environment following a release.
- ArgoCD: A continuous delivery solution that utilizes a declarative approach to manage applications. With Argo CD, I can ensure that the state of cluster is always aligned with the state defined in git workflow. This makes it a convenient tool for managing applications efficiently.

Grafana, in conjunction with monitoring solutions like Prometheus, offers real-time insights into the performance of applications. Teams can monitor key metrics such as CPU usage, memory usage, and response times, allowing them to quickly identify and resolve any performance issues. Additionally, utilizing Rancher as a container management platform provides access to logs, making it easier to debug applications and services within a cluster.

----

Managing secrets effectively involves the use of various tools and techniques, as secrets typically hold sensitive information such as passwords and encryption keys, which must be protected. Some of the tools I would use include:

- Harshicorp vault a widely used open-source secret management tool that integrates with Kubernetes. It offers secure storage, efficient management, and convenient access to secrets for multiple applications and services. It also come easy to integrate with Terraform for Infrastructure as Code.
- Using the public cloud secrets management solutions, such as AWS Secrets Manager or Google Cloud Secrets Manager, to manage secrets in Kubernetes clusters. At my current role, I and my team utilize AWS secret manager.


### *Describe your approach for deploying and managing “Addons” applications that enable, automate or facilitate setups on Kubernetes.*

My approach to deploying and managing Addons applications in Kubernetes is comprehensive and systematic. It starts with defining the specific requirements and functionalities that are needed for the services/applications to be deployed. This involves identifying the resources that are necessary to run the applications and the dependencies between them.

I then choose the appropriate tool that will help me achieve my goal, taking into consideration the budget allocated for the project. I thoroughly research the functionality, usage, and purpose of the Addons application before deploying it.

To ensure that I have a clear understanding of the setup and configuration, I create proper documentation for future references. This documentation includes detailed instructions on how to install, configure, and use the Addons application.

Security is a top priority for me when deploying and managing Addons applications. I take steps to ensure that the applications are secure and protected against attacks or exploits. I also consider the need for regular updates and upgrades to keep the applications running smoothly and efficiently.

In conclusion, my top priorities are security, stability, scalability and high availability.

