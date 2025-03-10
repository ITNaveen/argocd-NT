- so when argocd polls git commit and deploy in cluster, it will check if deployment is fine or not, if number of replicas are created or not.
1. healthy - if all the resources are 100% healthy.
2. degraded - if resources indicates unheathly and or not able to reach healthy status on timely manner.
3. missing - if resource is not present in the cluster.
4. suspended - if resource is suspended or paused, typical example of paused deployment.
5. unknown - health status is failed and health status is unknown.

- argocd also checks health check writtern in lua and these are defined in argocd-cm Configmap.

# DEMO - 
repo url - https://github.com/ITNaveen/gitops-argocd-kk.git
path - ./health-check
ns - health-check
create this app and then sync.

- access it on service:31374
- we have 4 shapes but trigle is not visible as its a white colour one and thats why we cant see that.
- we can now create a custom health check for this issue in lua scripting- 
  - scripting demo is visible in argocd website - https://argo-cd.readthedocs.io/en/latest/operator-manual/health/
  - Way 1. Define a Custom Health Check in argocd-cm ConfigMap¶
    Custom health checks can be defined in
    resource.customizations.health.<group>_<kind>: |
    we will use config map as group.
  - we need to edit cm in argocd-cm - 
    kubectl edit cm argocd-cm -n argocd

```yaml
apiVersion: v1
kind: ConfigMap
data:
  resource.customizations.health.ConfigMap: |
    hs = {}
    hs.status = "Healthy"    # initial healthcheck is healthy
     if obj.data.TRIANLE_COLOR =="white" then
        hs.status = "Degraded"
        hs.message = "Use a different color for TRIANGLE"
     end
    return hs
metadata:
  annotations:
    kubectl.kubernetes.io/last-applied-configuration: |
      {"apiVersion":"v1","kind":"ConfigMap","metadata":{"annotations":{},"labels":{"app.kubernetes.io/name":"argocd-cm","app.kubernetes.io/part-of":"argocd"},"name":"argocd-cm","namespace":"argocd"}}
  creationTimestamp: "2025-03-09T14:55:24Z"
  labels:
    app.kubernetes.io/name: argocd-cm
    app.kubernetes.io/part-of: argocd
  name: argocd-cm
  namespace: argocd
  resourceVersion: "454709"
  uid: e99ba6ad-a05d-4bf9-bc40-225138889e0e
  ```

  - now in argocd health status will show degraded.
  - this way we can set health checks.
  