# Argo CD

https://argo-cd.readthedocs.io/en/stable/getting_started/

1. [Install](#install)
2. [Login](#login)
3. [Helm Value](#helm-valuefile)
4. [Projects](#argo-cd-projects)
5. [Change Timezone](#change-timezone)

## Install

```bash
# Install
kubectl create namespace argocd
kubectl apply -n argocd -f https://raw.githubusercontent.com/argoproj/argo-cd/stable/manifests/ha/install.yaml

# Delete
kubectl delete -n argocd -f https://raw.githubusercontent.com/argoproj/argo-cd/stable/manifests/ha/install.yaml

# set context
kubectl config set-context --current --namespace=argocd
# namespace default
kubectl config set-context --current --namespace=default

# deploy app
argocd app create guestbook --repo https://github.com/argoproj/argocd-example-apps.git --path guestbook --dest-server https://kubernetes.default.svc --dest-namespace default
```

## Helm

```sh
helm repo add argo https://argoproj.github.io/argo-helm
helm upgrade --install argo-leapdevops argo/argo-cd -n argocd -f poc.yaml
kubectl -n argocd get secret argocd-initial-admin-secret -o jsonpath="{.data.password}" | base64 -d
```

## Login

```bash
# Linux install argocd cli
VERSION=$(curl -L -s https://raw.githubusercontent.com/argoproj/argo-cd/stable/VERSION)
curl -sSL -o argocd-linux-amd64 https://github.com/argoproj/argo-cd/releases/download/v$VERSION/argocd-linux-amd64
sudo install -m 555 argocd-linux-amd64 /usr/local/bin/argocd
rm argocd-linux-amd64

# First time login as admin
argocd admin initial-password -n argocd
# Login
argocd login <ARGOCD_SERVER>
# Change the password
argocd account update-password
```


## Deploy charts from helm repositories to argocd

https://spacelift.io/blog/argocd-helm-chart

```bash
argocd app set mysql --values values.yaml

argocd app create mysql \
	--repo https://charts.bitnami.com/bitnami \
	--helm-chart mysql \
	--revision 11.1.9 \
	--dest-server https://kubernetes.default.svc \
  --dest-namespace default
```

## Helm valueFile

1. click `new app` in argo ui
2. click `edit as yaml` copy content in `app.yaml`
3. copy content in `app.yaml` and place in `edit as yaml`
4. click `create`

> When you specify the sources field, Argo CD will ignore the source (singular) field.

```bash
apiVersion: argoproj.io/v1alpha1
kind: Application
metadata:
  name: mysql
spec:
  project: default
  # When you specify the sources field, Argo CD will ignore the source (singular) field.
  # source:
  #   path: charts
  #   repoURL: 'https://github.com/SahatasLee/ArgoCD.git'
  #   targetRevision: HEAD
  sources:
  - repoURL: 'https://charts.bitnami.com/bitnami'
    chart: mysql
    targetRevision: 11.1.14
    helm:
      valueFiles:
      - $values/charts/values.yaml
  - repoURL: 'https://github.com/SahatasLee/ArgoCD.git'
    targetRevision: HEAD
    ref: values
  destination:
    server: 'https://kubernetes.default.svc'
    namespace: default
```

## Argo CD Projects



## Change timezone

https://argocd-notifications.readthedocs.io/en/stable/templates/#change-the-timezone

```yml
kubectl -n argocd edit deployment argocd-notifications-controller
apiVersion: apps/v1
kind: Deployment
metadata:
  name: argocd-notifications-controller
spec:
(snip)
    spec:
      containers:
      - name: argocd-notifications-controller
        env:
        - name: TZ
          value: Asia/Tokyo
```