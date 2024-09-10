# Line Notifications

https://argocd-notifications.readthedocs.io/en/stable/services/webhook/#send-form-data

##

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: argocd-notifications-cm
data:
  template.github-commit-status: |
    webhook:
      <webhook-name>:
        method: POST # one of: GET, POST, PUT, PATCH. Default value: GET 
        path: <optional-path-template>
        body: key1=value1&key2=value2
```

## Example

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: argocd-notifications-cm
data:
  service.webhook.form: |
    url: https://notify-api.line.me/api/notify
    headers:
    - name: Content-Type
      value: application/x-www-form-urlencoded
    - name: Authorization
      value: Bearer $accessToken
  template.form-data: |
    webhook:
      form:
        method: POST
        body: |
            message="Failed to sync application {{.app.metadata.name}}."
```