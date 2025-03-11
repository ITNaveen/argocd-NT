# Typical helm chart structure - 
```yaml
helm-chart
├── Chart.yaml
├── templates
│   ├── NOTES.txt
│   ├── _helpers.tpl
│   ├── configmap.yaml
│   ├── deployment.yaml
│   ├── service.yaml
├── values.yaml
```
- typical example of deploying helm chart based app in argocd - 
```yml
argocd app create random-shapes \
  --repo https://github.com/ITNaveen/gitops-argocd-kk.git \
  --path helm-chart \
  --helm-set replicaCount=2 \
  --helm-set color.circle=pink \
  --helm-set color.square=violet \
  --helm-set service.type=NodePort \
  --dest-namespace helm \
  --dest-server https://kubernetes.default.svc
```

# argocd can also deploy from registries from bitnami by modyflying values in values.yaml - 
```yml
$ argocd app create nginx \
  --repo https://charts.bitnami.com/bitnami \
  --helm-chart nginx \
  --revision 12.0.3 \
  --values-literal-file values.yaml \
  --dest-namespace default \
  --dest-server https://kubernetes.default.svc
```
# also helm chart can be deployed via argocd UI.

# demo - 
1. i created helm namespace.
2. first check if git repo is giving 200 code - 
   curl -I https://github.com/ITNaveen/gitops-argocd-kk.git
3. if not then manully add that repo like - 
4. deployed app - 
```yml
argocd app create random-shapes \
  --repo https://github.com/ITNaveen/gitops-argocd-kk.git \
  --path helm-chart \
  --helm-set replicaCount=2 \
  --helm-set color.circle=pink \
  --helm-set color.square=violet \
  --helm-set service.type=NodePort \
  --dest-namespace helm \
  --dest-server https://kubernetes.default.svc
```
5. now its deployed in argocd.

###### we can also use bitnami chart staright for deployment ########
1. lets create a repo in argocd.
   go to settings - repo - connnect using https - name=bitnami-helm.
   repo_url = https://charts.bitnami.com/bitnami .
   now we have new helm type repo.
2. we will use nginx chart here.
   so from this helm repo, create an app - bitnami-helm-nginx-app .
   scrol down to sources and you will see - 
   repo url is bitnami, chart = select nginx and the version you like.
3. now we can see options to modify parameters as its helm repo. 
   service type = nodeport (as i am testing)
4. app is created, so sync it now.

- in CLI helm ls will bring nothing as this helm chart is manage by argocd.
- to check this then - argocd app get bitnami-helm-nginx-app .