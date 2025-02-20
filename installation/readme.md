# Install

## Manifest

```sh
kubectl create namespace argocd
# Non-HA
kubectl apply -n argocd -f https://raw.githubusercontent.com/argoproj/argo-cd/stable/manifests/install.yaml

# HA
# https://github.com/argoproj/argo-cd/blob/v2.13.1/manifests/ha/install.yaml
# https://raw.githubusercontent.com/argoproj/argo-cd/v2.13.1/manifests/ha/install.yaml
kubectl apply -n argocd -f https://raw.githubusercontent.com/argoproj/argo-cd/stable/manifests/ha/install.yaml
```

## Helm

```sh
helm repo add argo https://argoproj.github.io/argo-helm
helm upgrade --install argo-leapdevops argo/argo-cd -n argocd -f poc.yaml
kubectl -n argocd get secret argocd-initial-admin-secret -o jsonpath="{.data.password}" | base64 -d
```