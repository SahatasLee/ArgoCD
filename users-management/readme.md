# User Management

https://argo-cd.readthedocs.io/en/stable/operator-manual/user-management/

1. [Create User](#create-user)

## Create User

```yaml
# kubectl -n argocd edit cm argocd-cm
data:
    # Add new user
    account.newUsername: apiKey, login
```

## Configure RBAC for the Account

```sh
kubectl get configmap argocd-rbac-cm -n argocd -o yaml > argocd-rbac-cm.yml

data:
  policy.csv: |
    p, role:readonly, applications, get, *, allow
    g, newUsername, role:readonly

kubectl -n argocd apply -f argocd-rbac-cm.yml
```

## Create password for user

```sh
argocd account list
argocd account get --account <username>
# if you are managing users as the admin user, <current-user-password> should be the current admin password.
argocd account update-password \
  --account <name> \
  --new-password <new-user-password> \
  # --current-password <current-user-password> \
```

## Permission

https://www.redhat.com/en/blog/a-guide-to-using-gitops-and-argocd-with-rbac

```sh
p, example-user, applications, delete/*/namespace/*, *, deny
```