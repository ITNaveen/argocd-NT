- to check what projects we have so far, check - argocd proj list

- we can also do - 
kubectl get application -n argocd
NAME                   SYNC STATUS   HEALTH STATUS
solar-system-app-cli   Synced        Healthy

kubectl get appproj  -n argocd
NAME      AGE
default   20h

- lets create a project - 
  Go to UI - then to setting - project - new_project - add project name
  we can limit the scope by source repo (so i add my git repo here).

- now for project, i need to mention - 
  source repo - http://139.59.21.103:3000/siddarth/pod-metadata
  Destination - https://kubernetes.default.svc (choose the cluster)
  cluster resource allow list - 
  cluster resource deny list - ClusterRole , group = ""

# This is how it should look like - 
- argocd proj list
NAME                        DESCRIPTION                   DESTINATIONS                       SOURCES                                          
special-project             This project has restriction  https://kubernetes.default.svc,*   http://139.59.21.103:3000/siddharth/pod-metadata  <none>                      <none>                        <none>          disabled            <none>

- or project details can be seen - argocd proj get special-project

- to get this in jason - argocd proj get special-project -o yaml
  
# now project has been created, lets create an app in this project - 
Go to settings - repo - connect - then choose https type git and project = special-project (just created)
repo successfully connected.

- now create app - 
  new_app - name then add project (special-project), auto-create namespace, deploy in same cluster and namespace = default, dir is manifests
  due to cluster-role denied issue, app wouldnt sync.
  so go to sync again and uncheck - clusterole.

# thats how i can create app in project - 
- i can have allow list or deny list for specific cluster.
- restriction for repo, destination target.
