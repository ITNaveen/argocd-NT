# creating app using UI - 

- application - new_app - manual_sync 
- now we need to connect repo - setting - repo - connect using https - then git repo url
  we need git username and PAT.
- now if i do - kubectl get secrets -n argocd, then i see repo secret and thats based on our new repo.
- lets check whats in this scret - kubectl get secret repo-2421535171 -n argocd -o json
  to see if thats the same repo - 
  kubectl get secret repo-2421535171 -n argocd -o json | jq .data.url -r | base64 -d (my repo will be visible)

# now create an app - 
- solar-system-app-1 - manual sync - select repo - then add path like here "solar-system" , so ./solar-system
- then define target (k8s service) - solar-system(namespace i created) - create
- we will see "missing" and "out of sync" status.
- argocd has detected and connected and can see it needs to deploy service and deployment. so far we dont have solar-system namespace or 
  any service deployed on k8s cluster.
- i can click on sync in app ui and it will show me what to sync, click on syncronize and then i see sync failed.
  Because namespace was not created so do sync again and click namspace create.
- now all sync and good and we can check - kubectl get all -n solar-system
- i can check now with svc url and see app is deployed now.

# update and rollback
- app is working fine but i need to update.
- i updated image image in github and soon after argocd is showing out of sync, so refresh and then sync and we have new deployement.
- lets say we need to rollback then - 
   - click on history and rollback in app ui.
   - i can see previous deployment and then 3 dots click on rollback and thats it.


# crating app using CLI - 
- argocd app create
```yaml
argocd app create solar-system-app-CLI \
  --repo https://github.com/ITNaveen/gitops-argocd-kk.git \
  --path solar-system \
  --dest-server https://kubernetes.default.svc \
  --dest-namespace solar-system
```
CREATED.

- app is created but its not sync, so lets sync this - 
  argocd app sync app_name  (app is synced)

- argocd app list (app is healthy and synced)

- check now - kubectl get all and check service of solar-system and then with port number open that in browser.

- delete this app by UI - argocd app delete solar-system-app




  
