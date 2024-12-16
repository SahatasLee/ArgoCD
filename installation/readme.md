# Install

```sh
kubectl create namespace argocd
# Non-HA
# kubectl apply -n argocd -f https://raw.githubusercontent.com/argoproj/argo-cd/stable/manifests/install.yaml
# HA
# https://github.com/argoproj/argo-cd/blob/v2.13.1/manifests/ha/install.yaml
# https://raw.githubusercontent.com/argoproj/argo-cd/v2.13.1/manifests/ha/install.yaml
kubectl apply -n argocd -f https://raw.githubusercontent.com/argoproj/argo-cd/stable/manifests/ha/install.yaml
```