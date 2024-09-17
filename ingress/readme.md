# Traefik Ingress

traefil version 2.7.x

https://argo-cd.readthedocs.io/en/stable/operator-manual/ingress/#option-2-ssl-termination-at-ingress-controller

1. Config `argocd-cmd-params-cm` configmap

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: argocd-cmd-params-cm
data:
  server.insecure: "true"
```

This will install ArgoCD and configure the argocd-server deployment to use the --insecure flag as needed to stop Argo from handling the TLS termination itself and giving that responsibility to Traefik.

```sh
# Create configmap
kubectl -n argocd create -f ingress/argocd-cmd-params-cm.yaml

# Recreate argocd-server to load config
kubectl -n argocd delete cm argocd-server
```

2. Create Ingress

```sh
kubectl -n argocd create -f ingress.yaml
```