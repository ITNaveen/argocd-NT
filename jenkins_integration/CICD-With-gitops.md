we have 2 repos.
1. app code repo.
2. kubernetes manifests repo.

# argocd - 
- argocd pulls manifest from git repo (kubernetes manifests repo).
- it will create or deploy resource on the cluster.

###### ######## ########## ######## DEMO ######### ########## ######### ##########

1. source repo - https://github.com/ITNaveen/argocd-app-sourcecode-KK.git
2. k8s repo - https://github.com/ITNaveen/gitops-argocd-kk.git

# add creds to jenkins - 
1. argocd creds - argocd-token.
- argocd login localhost:31592 --username admin --password @sl193702C.
- kubectl edit configmap argocd-cm -n argocd.
- then after this edit. 
- it should have.
```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: argocd-cm
  namespace: argocd
data:
  accounts.admin: apiKey,login
```
- then generate token - argocd account generate-token --account admin.
- in jenkins - creds - secret text then paste it.

2. docker hub creds pasted as well - docker-hub.

3. git credentials.
- username and password.

