# Argo CD

https://argo-cd.readthedocs.io/en/stable/getting_started/

```bash
# Install
kubectl create namespace argocd
kubectl apply -n argocd -f https://raw.githubusercontent.com/argoproj/argo-cd/stable/manifests/install.yaml

# Delete
kubectl delete -n argocd -f https://raw.githubusercontent.com/argoproj/argo-cd/stable/manifests/install.yaml

# set context
kubectl config set-context --current --namespace=argocd
# namespace default
kubectl config set-context --current --namespace=default

# deploy app
argocd app create guestbook --repo https://github.com/argoproj/argocd-example-apps.git --path guestbook --dest-server https://kubernetes.default.svc --dest-namespace default
```

## Login

```bash
argocd login <ARGOCD_SERVER>
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
  source:
    path: charts
    repoURL: 'https://github.com/SahatasLee/ArgoCD.git'
    targetRevision: HEAD
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