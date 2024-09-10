# Notification

[Argo CD Notifications Docs](https://argocd-notifications.readthedocs.io/en/stable/)

## Installed

https://argocd-notifications.readthedocs.io/en/stable/

```sh
# Install Argo CD Notifications
kubectl apply -n argocd -f https://raw.githubusercontent.com/argoproj-labs/argocd-notifications/release-1.0/manifests/install.yaml

root@ansible-server:~# kubectl apply -n argocd -f https://raw.githubusercontent.com/argoproj-labs/argocd-notifications/release-1.0/manifests/install.yaml
serviceaccount/argocd-notifications-controller configured
role.rbac.authorization.k8s.io/argocd-notifications-controller configured
rolebinding.rbac.authorization.k8s.io/argocd-notifications-controller configured
configmap/argocd-notifications-cm configured
secret/argocd-notifications-secret configured
service/argocd-notifications-controller-metrics configured
deployment.apps/argocd-notifications-controller configured

# Install Triggers and Templates from the catalog
kubectl apply -n argocd -f https://raw.githubusercontent.com/argoproj-labs/argocd-notifications/release-1.0/catalog/install.yaml

root@ansible-server:~# kubectl apply -n argocd -f https://raw.githubusercontent.com/argoproj-labs/argocd-notifications/release-1.0/catalog/install.yaml
configmap/argocd-notifications-cm configured

# Config Email
kubectl patch cm argocd-notifications-cm -n argocd --type merge -p '{"data": {"service.email.gmail": "{ username: $email-username, password: $email-password, host: smtp.gmail.com, port: 465, from: $email-username }" }}'

# Example
kubectl patch cm argocd-notifications-cm -n argocd --type merge -p '{"data": {"service.email.email": "{ host: 10.111.0.249, port: 25, from: sahatas@example.com }" }}'
```

## Email

https://argocd-notifications.readthedocs.io/en/stable/services/email/

###

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: argocd-notifications-cm
data:
  service.email.example: |
    host: smtp.example.com
    port: 587
    from: $email-username
```

### Subscriptions

https://argocd-notifications.readthedocs.io/en/stable/subscriptions/

https://argo-cd.readthedocs.io/en/stable/operator-manual/notifications/subscriptions/

The subscription to Argo CD application events can be defined using notifications.argoproj.io/subscribe.<trigger>.<service>: <recipient> annotation.

- Subscribe to notifications by adding the notifications.argoproj.io/subscribe.on-sync-succeeded.email annotation to the Argo CD application or project:

```sh
# Get app
kubectl get app -n argocd

# Subscribe
kubectl patch app <my-app> -n argocd -p '{"metadata": {"annotations": {"notifications.argoproj.io/subscribe.on-sync-succeeded.email":"<my-email>"}}}' --type merge
```

#### Default Subscriptions

The subscriptions might be configured globally in the argocd-notifications-cm ConfigMap using subscriptions field. The default subscriptions are applied to all applications. The trigger and applications might be configured using the triggers and selector fields:

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: argocd-notifications-cm
data:
  # Contains centrally managed global application subscriptions
  subscriptions: |
    # subscription for all trigger notifications
    - recipients:
      - email:test@example.com
      - slack:test2
      triggers:
      - on-sync-status-unknown
      - on-created
      - on-deleted
      - on-deployed
      - on-health-degraded
      - on-sync-failed
      - on-sync-running
      - on-sync-succeeded
    # subscription restricted to applications with matching labels only
    - recipients:
      - slack:test3
      selector: test=true
      triggers:
      - on-sync-status-unknown
```

```yaml
apiVersion: v1
data:
  service.email.email: |
    # username: $email-username
    # password: $email-password
    host: 10.111.0.249
    port: 25
    from: sahatas@leapgio.cloud
    insecure_skip_verify: true
  subscriptions: |
    # subscription for on-sync-status-unknown trigger notifications
    - recipients:
      - email:Sahatas.L@leapsolutions.co.th
      - email:sahataslee@outlook.co.th
      - email:peerapong.p@leapsolutions.co.th
      triggers:
      - on-sync-status-unknown
      - on-created
      - on-deleted
      - on-deployed
      - on-health-degraded
      - on-sync-failed
      - on-sync-running
      - on-sync-succeeded
```

## App

```sh
kubectl -n argocd get app
```

## Argo CD Notifications Secret

```sh
export EMAIL_USER=<your-username>
export PASSWORD=<your-password>
kubectl apply -n argocd -f - << EOF
apiVersion: v1
kind: Secret
metadata:
  name: argocd-notifications-secret
stringData:
  email-username: $EMAIL_USER
  email-password: $PASSWORD
type: Opaque
EOF
```

slack

```yaml
apiVersion: v1
kind: Secret
metadata:
  name: argocd-notifications-secret
stringData:
  slack-token: <auth-token>
```

email

```yaml
apiVersion: v1
kind: Secret
metadata:
  name: argocd-notifications-secret
stringData:
  email-username: $EMAIL_USER
  email-password: $PASSWORD
```

slack and email

```yaml
apiVersion: v1
kind: Secret
metadata:
  name: argocd-notifications-secret
stringData:
  email-username: $EMAIL_USER
  email-password: $PASSWORD
  slack-token: <auth-token>
```

Update an Existing Secret

```sh
# Encode value
echo -n 'new-value' | base64

# Add new value
# Value must be base64-encoded.
kubectl patch secret argocd-notifications-secret -p '{"stringData": {"new-key": "new-value-base64"}}' -n argocd --type merge

# Create new secret
kubectl create secret generic argocd-notifications-secret \
  --from-literal=email-username=$EMAIL_USER \
  --from-literal=email-password=$PASSWORD \
  --from-literal=slack-token=<auth-token> \
  --from-literal=line-token=new-value \
  -n argocd \
  --dry-run=client -o yaml > updated-secret.yaml
```