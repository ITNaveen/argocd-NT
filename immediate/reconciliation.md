- This function try to match actual state in k8s cluster from desired state in git.
- Reconciliation loop means how often your argocd will sync with git repo.
- normally "argocd-server"(responsible for fetching the desired state from git) has default time for Reconciliation interval 3 min.
  this can be configured via argocd-cm by increasing timeout.

# Reconciliation loop - webhook - 
- by using webhook (/api/webhook), we can use another way for argocd-server to update the cluster. 
- using this enable github that everytime we have a push to github, it sends events to argocd-server then in return argocd-repo-server 
  will pull the committed changes.

1. argocd-server (Handles Webhook Events)
When you configure a webhook in your Git repository (e.g., GitHub, GitLab), it sends a notification to the argocd-server whenever there is a commit or a change in the repository.
    The argocd-server is the API server responsible for managing Argo CD's operations. It listens for webhook events from Git repositories.
    When a commit is pushed, the webhook sends an HTTP request to the argocd-server (specifically to the /api/webhook endpoint).

2. argocd-repo-server (Fetches and Syncs Changes)
Once argocd-server receives the webhook event, it triggers the sync process for the related Application. To do so, it coordinates with the argocd-repo-server.
    The argocd-repo-server is the component responsible for interacting with Git repositories and retrieving the actual repository contents (e.g., manifests, Helm charts, etc.).
    After the webhook event triggers the sync process, the argocd-server communicates with the argocd-repo-server to fetch the latest commit or revision from the Git repository.
    argocd-repo-server pulls the latest commit (or the specified revision) from the Git repository, compares it with the desired state (i.e., what is already deployed in the cluster), and determines if there is any drift.

# DEMO - 
- for this i can use gitops-argocd-kk and path "./nginx-app" and then create this in "webhook" namespace (creating new app).
- now i made changes in gitrepo and see no changes in deployment as polling interval is 3 min.
- now i can change the polling time.
  - kubectl describe pod argocd-repo-server-6bf8b458cb-zztgn -n argocd | grep "recon" 

      ARGOCD_RECONCILIATION_TIMEOUT:                                <set to the key 'timeout.reconciliation' of config map 'argocd-cm'>                                          Optional: true
    so it can be seen that "ARGOCD_RECONCILIATION_TIMEOUT" is set but it takes data "timeout.reconciliation" from argocd cm.
    this means for me change this timout, i need to set "timeout.reconciliation" in argocd-cm and then add time.
    - kubectl get cm argocd-cm -n argocd -o yaml (now you can update this).
this is manual way to set reconciliation.

# we need to set this by push event in repo - 
- go to git repo then webhook and paste argocd server url (https://localhost:31592) in payload then "/api/webhook".
- 