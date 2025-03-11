Default argocd has one build in admin user with full access of the system.
🔹 Local users Stored inside ArgoCD (for small setups).
🔹 SSO Users (Single Sign-On) Login via GitHub, LDAP, OIDC, etc. (for teams).

# local user management - 
1. add user - 
```yaml
   kubectl -n argocd patch secret argocd-secret \
   -p '{"stringData": {"accounts.my-user": "plaintextpassword"}}' # we created my-user with password. 
```

2. edit "argocd-rbac-cm" cm in agocd ns.
```yml
apiVersion: v1
kind: ConfigMap
metadata:
  name: argocd-rbac-cm
  namespace: argocd
data:
  policy.csv: |
    p, my-user, applications, get, *   # User can only view apps
```

3. apply change - 
kubectl apply -f argocd-rbac-cm.yaml.
kubectl rollout restart deployment argocd-server -n argocd.


# adding user to group - 
1. add user to group. (This adds my-user to the dev-team group.)
```yaml
   kubectl -n argocd patch secret argocd-secret \
   -p '{"stringData": {"accounts.my-user": "plaintextpassword", "accounts.my-user.groups": "dev-team"}}'
```

2. apply and restart argocd - 
```yaml
kubectl apply -f argocd-rbac-cm.yaml
kubectl rollout restart deployment argocd-server -n argocd
```
