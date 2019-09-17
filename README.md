# Argo-CD with Kustomize

## Goal

Within Argo-CD an application should be configured using the overlay/xy/applications.
These applications are described within the sub-path res. As sample an simple nginx is used.
For the development the amount of replicas and the namespace should be changed.

## Creating dev application & resources via kustomize

`kustomize build ./overlay/dev/applications`
````
apiVersion: argoproj.io/v1alpha1
kind: Application
metadata:
  name: development-nginx-service
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

`kustomize build ./overlay/dev/res/nginx`
````
apiVersion: apps/v1
kind: Deployment
metadata:
  name: development-nginx-deployment
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

