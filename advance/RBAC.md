- Access control on argocd resources are posible because of RBAC policies.
- policies are defined as csv format within "argocd-rbac-cm" configmap in argocd ns.
- policies are assighn to user or sso group.
  format - 
```yml
     p, <subject>, <resource>, <action>, <object>
     p = Policy definition
     <subject>* **User, group, or service account
     <resource> → What resource is being controlled (applications, clusters, projects, etc.)
     <action> → What action is allowed (get, create, update, delete, sync, override, admin, etc.)
     <object>* **The specific resource being acted upon ( *for all resources)
```
# example - 
```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: argocd-rbac-cm
  namespace: argocd
data:
  policy.default: role:readonly  # Default role for users (if not explicitly assigned)
  policy.csv: |
    p, admin-user, applications, *, *
    p, dev-user, applications, get, my-app
    p, qa-user, applications, sync, my-app
    p, team-lead, clusters, get, *
    p, team-lead, projects, create, *
```
- Breaking Down the Policy Rules
admin-usercan perform ALL actions (*) on ALL applications (*).
dev-usercan only get(read) the application my-app.
qa-usercan only sync(deploy) my-app.
team-leadcan get(read) all clusters.
team-leadcan createnew projects.

# to update.
kubectl edit configmap argocd-rbac-cm -n argocd.

# restart after update . 
kubectl rollout restart deployment argocd-server -n argocd.

# verify if a user has correct permissions.
argocd auth can-i get applications --as dev-user.

# basic clasification - 
```yaml
Cluster (my_app)  # The Kubernetes environment where apps run
 ├── Project (team-project-1)  # Groups apps & enforces access control
 │    ├── Application (frontend)  # A deployable unit (e.g., service)
 │    ├── Application (backend)
 │    ├── Application (database)
 │
 ├── Project (team-project-2)  # Another isolated group of apps
      ├── Application (analytics)
      ├── Application (reporting)
```