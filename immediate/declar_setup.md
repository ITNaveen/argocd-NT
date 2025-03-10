so we have a root dir - declarative dir - manifests - geocentric-model (our app)
we can define this is kubernetes as - 
# --- YAML START ---
# yaml-language-server: $schema=https://json.schemastore.org/kubernetes
apiVersion: argoproj.io/v1alpha1
kind: Application
metadata:
  name: geocentric-model-app
  namespace: argocd
spec:
  project: default
  source:
    repoURL: https://github.com/sidd-harth/test-cd.git
    targetRevision: HEAD
    path: ./declarative/manifests/geocentric-model
  destination:
    server: https://kubernetes.default.svc
    namespace: geocentric-model
  syncPolicy:
    syncOptions:
      - CreateNamespace=true
    automated:
      selfHeal: true
# --- YAML END ---
