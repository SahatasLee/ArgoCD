# Ingress

- Traefik
- nginx

## Traefik

traefik version 2.6.x

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
kubectl -n argocd apply -f ingress/argocd-cmd-params-cm.yaml

# Recreate argocd-server to load config
kubectl -n argocd rollout restart deployment argocd-server
```

2. Create IngressRoute

https://doc.traefik.io/traefik/v2.6/routing/providers/kubernetes-crd/#kind-tlsstore
https://argo-cd.readthedocs.io/en/stable/operator-manual/ingress/#ingressroute-crd

```sh
# Create tls secret
kubectl -n argocd create -f tls-secret.yaml
# Create tls store
kubectl -n argocd create -f tls-store.yaml
# Create ingress route
kubectl -n argocd create -f ingressroute.yaml
```

3. Create Ingress (Optional)

```sh
kubectl -n argocd create -f ingress.yaml
```

## Nginx

