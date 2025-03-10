this is the way on how it sync from git to target state in cluster, when it discover newer version of app in git then it will perform.
1. manual sync.
2. automatic sync.

# auto-pruning of resources - 
Auto-pruning in ArgoCD ensures that deleted resources from your Git repository are automatically removed from the cluster during synchronization.
this is how it needs to be defined - 
syncPolicy:
    automated:
      prune: true  

- to enable this from CLI - 
  argocd app sync my-app --prune

⚠ Auto-pruning will permanently delete resources.
    Use prune: false if you want to manually handle deletions.
    Test in staging before enabling in production.

# self healing - 
Self-healing in ArgoCD ensures that any manual changes made to resources in the cluster are automatically reverted to match the Git state.
syncPolicy:
    automated:
      selfHeal: true 

