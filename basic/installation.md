two types of installations way - 
1. multi tenant. 
2. core.  (excludes api server and ui)

ArgoCD offers two main installation types:

1️⃣ Core (Single-Tenant) → Simpler setup, usually for small teams or individual applications.
2️⃣ Multi-Tenant → Used for shared Kubernetes clusters where multiple teams deploy applications.

Core vs. Multi-Tenant Installation
Installation Type	        High Availability (HA)	                Best For
Core (Single-Tenant)	    ❌ No	                                Small teams, simple projects
Multi-Tenant (Non-HA)	    ❌ No	                                Shared clusters, but without high availability
Multi-Tenant (HA)	        ✅ Yes	                                Large production systems needing reliability

# 1️⃣ Core (Single-Tenant) Installation
A basic installation with no high availability.
Good for small setups or dev/test environments.
Installed using:
install.yaml
Can be namespace-scoped using:
namespace-install.yaml

# 2️⃣ Multi-Tenant Installation
Multi-Tenancy means multiple teams (tenants) share the same ArgoCD installation while keeping their workloads isolated.
There are two options within Multi-Tenant mode:

- 🔹 Non-High Availability (Non-HA)
Multi-Tenant, but not highly available.
If a pod crashes, ArgoCD might go down temporarily.
Installed using:
install.yaml

- 🔹 High Availability (HA)
Fully redundant and scalable setup.
Uses multiple ArgoCD replicas across nodes for zero downtime.
Installed using:
ha/install.yaml
Namespace-scoped version:
ha/namespace-install.yaml

💡 Summary
Core (Single-Tenant) → Simple setup, no HA.
Multi-Tenant (Non-HA) → Shared cluster, but no redundancy.
Multi-Tenant (HA) → Best for production, full redundancy & reliability.


##### Execution - 
- getting started argocd doc then go to the right and click on download.
- then see github release and we get github repo (atm v2.14.4).
- HA and non HA version is here - 
  1. i pick non-HA.
  kubectl create namespace argocd
  kubectl apply -n argocd -f https://raw.githubusercontent.com/argoproj/argo-cd/v2.14.3/manifests/install.yaml
  2. we need to have k8s cluster already, check namespaces.
  3. kubectl get all -n argocd (checking all in argocd namepsace), we have alot of pod and svc.
  4. we have "argocd-server" as main one we need.
  5. edit that - kubectl edit svc argocd-server -n argocd, then change type to "NodePort".
  6. now open it in ip:port provided.
  7. when ui is live then - kubectl get secrets -n argocd, i see initial admin secret.
  8. kubectl describe secret argocd-initial-admin-secret -n argocd , then decode this - echo "BASE64_ENCODED_PASSWORD" | base64 --decode
     or - kubectl get secret argocd-initial-admin-secret -n argocd -o=jsonpath='{.data.password}' | base64 --decode
  9. so admin, then password and you are in.

  # lets install CLI as well - 
  1. go to your release and check linux amd64.(in assets part).
  2. in k8s cluster do wget and paste that.
  3. once done do ls and we should have amd64 there, rename this to argocd.
  4. make this executable by - chmod +x argocd
  5. mv this to /use/local/bin dir.
  6. argocd should be up now.
  7. to login i need - argocd login serverIP(ip from svc for argocd-server).
  8. then username and password.
  9. check "argocd app list", argocd cluster list and we will see our default k8s cluster.

