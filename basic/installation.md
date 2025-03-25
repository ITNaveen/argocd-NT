two types of installations way - 
1. multi tenant. 
2. core.  (excludes api server and ui)

ArgoCD offers two main installation types:

1️⃣ Core (Single-Tenant) → Simpler setup, usually for small teams or individual applications.
2️⃣ Multi-Tenant → Used for shared Kubernetes clusters where multiple teams deploy applications.

Core vs. Multi-Tenant Installation
Installation Type	        High Availability (HA)	                Best For
Core (Single-Tenant)	    ❌ No	                                   Small teams, simple projects
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

#  🔹 Non-High Availability (Non-HA)
Multi-Tenant, but not highly available.
If a pod crashes, ArgoCD might go down temporarily.
Installed using:
- install.yaml - 
1. provide standard installation with cluster admin access of argocd. if you want to deploy application on the same cluster where argocd is running, 
then use this yaml.
2. you can still deploy application to remote cluster as well.
- namespace-install.yaml - this provide installation that only require namespace level access.

................................
................................
# 🔹 High Availability (HA)
Fully redundant and scalable setup.
Uses multiple ArgoCD replicas across nodes for zero downtime.
- Installed using:

ha/install.yaml
1. This YAML file contains the actual ArgoCD installation resources for HA mode, such as deployment configurations for ArgoCD components, service configurations, persistence settings, etc.
2. It includes the setup for making ArgoCD highly available by deploying multiple replicas of its components (like the API server, controller, and repository server).
3. It may also include configurations for things like persistent storage for stateful components (e.g., Redis, Postgres) and load balancing for better distribution of traffic across ArgoCD components.
4. Why to use: You use this file after you've set up the namespace to deploy the ArgoCD components in a highly available configuration, where the resources are replicated to ensure reliability and fault tolerance.

Namespace-scoped version:
ha/namespace-install.yaml
1. This YAML file typically contains the configuration to create the namespace and install the ArgoCD components necessary for HA in that namespace.
2. It's usually the first file you use when installing ArgoCD in HA mode.
3. It will set up the necessary namespace (e.g., argocd), define the resources, and maybe configure services or persistent volumes that the HA setup will use.
4. Why to use: You use this to create the required namespace and some essential resources before you apply the actual HA configurations.

The Process:
1. Install namespace and setup resources:
- First, you'll need to set up the environment with the namespace and any necessary resources.
- Use the ha/namespace-install.yaml to do this.

Install ArgoCD with HA:
- Once the environment is ready, apply the ha/install.yaml to actually install ArgoCD with its HA configuration.

💡 Summary
Core (Single-Tenant) → Simple setup, no HA.
Multi-Tenant (Non-HA) → Shared cluster, but no redundancy.
Multi-Tenant (HA) → Best for production, full redundancy & reliability.

.....................................................
.....................................................

##### Execution - 
- getting started argocd doc then go to the right and click on - download_argo_cd_cli - from middle top go to github repo - find the version you need .
- based on the version, click on the release.
- pick HA or non HA version from here. 
  1. i pick non-HA.
  kubectl create namespace argocd
  kubectl apply -n argocd -f https://raw.githubusercontent.com/argoproj/argo-cd/v2.14.3/manifests/install.yaml
  2. we need to have k8s cluster already, check namespaces.
  3. kubectl get all -n argocd (checking all in argocd namepsace), we have alot of pod and svc.
  4. we have "argocd-server" as main svc we need.
  5. edit that - kubectl edit svc argocd-server -n argocd, then change type to "NodePort".
  6. now open it in ip:port provided.
  7. ui is up and now we can type username= admin but password ?
  8. kubectl get secrets -n argocd, i see initial admin secret.
  9. kubectl get secret argocd-initial-admin-secret -n argocd -o json now we have password, so conver this to base64.
  10. echo 'MWk4UkRhY3RJWG40YlJFMQ==' | base64 --decode
  11. so admin, then password and you are in.
  12. from user info in argocd update your password.

  # lets install CLI as well - 
  1. go to your release and check linux amd64.(in assets part).
  2. in k8s cluster do wget and paste that, this way argocd-linux-amd64 is downloaded.
  3. once done do ls and we should have amd64 there, rename this to argocd.
  4. make this executable by - chmod +x argocd
  5. mv this to /use/local/bin dir.
  6. argocd should be up now.
  7. to login i need - argocd login serverIP(ip from svc for argocd-server).
  8. then username and password.
  9. check "argocd app list", argocd cluster list and we will see our default k8s cluster.

