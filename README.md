# Argo-CD with Kustomize

## Goal

Within Argo-CD an application should be configured using the overlay/xy/applications.
These applications are described within the sub-path res. As sample an simple nginx is used.
For the development the amount of replicas and the namespace should be changed.

## Checking dev overlay application & resources via kustomize

+ `kustomize build ./overlay/dev/applications`

````
apiVersion: v1
kind: Namespace
metadata:
  name: dev
---
apiVersion: argoproj.io/v1alpha1
kind: Application
metadata:
  name: nginx-service
  namespace: argocd
spec:
  destination:
    namespace: dev
    server: https://kubernetes.default.svc
  project: default
  source:
    path: overlay/dev/res/nginx
    repoURL: https://github.com/Mathew1988/argo-kustomize
    targetRevision: HEAD
  syncPolicy:
    automated: {}
````

+ `kustomize build ./overlay/dev/res/nginx`

````
apiVersion: apps/v1
kind: Deployment
metadata:
  name: nginx-deployment
spec:
  replicas: 1
  selector:
    matchLabels:
      app: nginx
  template:
    metadata:
      labels:
        app: nginx
    spec:
      containers:
      - image: nginx:1.7.9
        name: nginx
        ports:
        - containerPort: 80
````

## Adding the dev application to Argo-CD

````
argocd app create dev-app \
  --dest-server https://kubernetes.default.svc \
  --repo https://github.com/Mathew1988/argo-kustomize \
  --path overlay/dev/applications \
  --dest-namespace argocd
````
