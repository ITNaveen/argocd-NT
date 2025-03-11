Instead of writing multiple ArgoCD Application manifests for each app, you can use the App of Apps pattern to manage everything from one single manifest.
- this way i will deploy 1 application and this in turn will deploy multiple applications.

- here is the structure first to understand - 
```yml
root 
    - declarative 
        - app-of-apps (this is dir structure).
            - geocentric
            - heliocentric
            - heliocentric-no-pluto
        - manifests
            - geocentric   # each of these dir has their own manifest files
                - svc
                - deploy
            - heliocentric
                - svc
                - deploy
            - geocentric-no-pluto
                - svc
                - deploy
        - multi-app
            - app-of-apps.yaml

# and here is this yaml - 

apiVersion: argoproj.io/v1alpha1
kind: Application
metadata:
  name: app-of-apps
  namespace: argocd
spec:
  project: default
  source:
    repoURL: http://165.22.209.118:3000/siddharth/gitops-argocd.git   #source repo.
    targetRevision: HEAD
    path: ./declarative/app-of-apps # this is where all my apps are.
  destination:
    server: https://kubernetes.default.svc
    namespace: argocd
  syncPolicy:
    automated:
      prune: true
      selfHeal: true
```
- now within "app-of-apps" we have argocd yaml for each app and then also we have dir for each of these app (with deploy and svc).
- a typical "geocentric-app.yaml" looks like this - 
```yaml
apiVersion: argoproj.io/v1alpha1
kind: Application
metadata:
  name: geocentric-app
  namespace: argocd
  finalizers:
    - resources-finalizer.argocd.argoproj.io
spec:
  project: default

  source:
    repoURL: http://165.22.209.118:3000/siddharth/gitops-argocd.git
    targetRevision: HEAD
    path: ./declarative/manifests/geocentric-model
   
  destination:
    server: https://kubernetes.default.svc
    namespace: geocentric

  syncPolicy:
    syncOptions:
      - CreateNamespace=true  
    automated:
      prune: true
      selfHeal: true
```
- now we can deploy these 3 apps using app-of-apps way.
we should have this repo in local as well then - 
k apply -f app-of-apps.yaml -n argocd
now this will apply all 3 (and app-of-apps as well) apps in argocd.
- this app-of-apps will show that its managing all three of these apps.

