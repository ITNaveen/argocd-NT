# argocd can deploy application to external cluster, where its not even installed and its called multi cluster deployment model.
1. for this we need 2 cluster.
   demo cluster.
   dev-space cluster.

2. we need to deploy app to dev-spcace cluster.

3. lets check how many target clusters we have.
   argocd cluster list (we have only 1 atm, this is the same where my argocd is deployed).

4. first we need to define this second cluster in config file.
   - kubectl config get-context (list all available clusters in your kubeconfig).
   - kubectl cluster-info (detailed cluster info).

5. once the context is define then i do - 
   argocd cluster add new_cluster_context
   argocd cluster add admin@devspace (with this argocd is ready to deploy in second cluster dev-space as well).
   this will create service account, clusterole, cluster-rolebinding as well.

6. now check clusters list - argocd cluster list (it will show both clusters).

# so argocd has added this context but where exactly argocd stored this - 
- argocd store this new cluster details as secret, so if i do - 
  argocd get secrets -n argocd (i will see new secret for this cluster just added), normally cluster ip.

- lets see what stored in this secret - 
  argocd get secrets cluster-165.22.209.118-2127792194 -n argocd -o json 
  it has name of the server, certificate of sever etc.
  - just copy the value from server and decode it using base64 and we get ip address and port of the new (second) cluster.

# now deploy app in this new cluster as well - 
1. from UI - add app - name=multi-app-demo , default, auto sync policy, auto namepace create.
   pick source repo - then copy the path of app from git repo .
2. for destination we have now second cluster visible as well.
   pick your cluster, make new namespace.
3. created.
   



