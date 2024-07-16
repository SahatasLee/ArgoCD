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