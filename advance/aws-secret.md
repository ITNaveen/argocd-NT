lets see how argocd valult pluings fetch secrets from hashicorp vault.
- argocd vault plugins is custom one that can inject secret to kubernetes resources after retrieving them from various sources.

# steps in theory -
1. Store the secrets in AWS Secrets Manager using the console or CLI.
2. Install the ArgoCD Vault Plugin by updating the ArgoCD ConfigMap with the plugin configuration.
3. Create an IAM policy with minimal permissions (GetSecretValue, DescribeSecret) for accessing specific secrets.
4. Configure AWS authentication by adding AWS credentials as environment variables to the ArgoCD repo server deployment.
5. Use secret placeholders in my Kubernetes YAML files with the syntax <path:secret-name#key> to reference AWS secrets.
6. Create an ArgoCD Application that uses the vault plugin in its source configuration.
7. When ArgoCD syncs, the plugin will replace placeholders with actual secret values from AWS before applying to the cluster.

This approach keeps secrets secure by not storing them in Git and dynamically injecting them at deployment time.

# Store Secrets in AWS Secrets Manager.
Create secret in AWS console or AWS CLI.
Example: aws secretsmanager create-secret --name my-app-secrets --secret-string '{"DB_PASSWORD":"password123","API_KEY":"key123"}' 

# install argocd vault plugin
```yml
apiVersion: v1
kind: ConfigMap
metadata:
  name: argocd-cm
  namespace: argocd
data:
  configManagementPlugins: |
    - name: argocd-vault-plugin
      generate:
        command: ["argocd-vault-plugin"]
        args: ["generate", "./"]
```

# Set Up AWS Authentication for argocd vault , creating an IAM policy for access to secret.
```yml
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Effect": "Allow",
      "Action": [
        "secretsmanager:GetSecretValue",
        "secretsmanager:DescribeSecret"
      ],
      "Resource": "arn:aws:secretsmanager:*:*:secret:my-app-secrets*"
    }
  ]
}
```

# modify argocd server deploy to use aws creds
```yml
env:
- name: AWS_ACCESS_KEY_ID
  valueFrom:
    secretKeyRef:
      name: aws-credentials
      key: access-key
- name: AWS_SECRET_ACCESS_KEY
  valueFrom:
    secretKeyRef:
      name: aws-credentials
      key: secret-key
- name: AWS_REGION
  value: us-west-2
```

# Use Secret Placeholders in YAML.
```yaml
apiVersion: v1
kind: Secret
metadata:
  name: my-app-secret
  annotations:
    avp.kubernetes.io/path: "my-app-secrets"
type: Opaque
data:
  password: <path:my-app-secrets#DB_PASSWORD>
  apikey: <path:my-app-secrets#API_KEY>
```

# create argocd app - 
```yml
apiVersion: argoproj.io/v1alpha1
kind: Application
metadata:
  name: my-app
spec:
  source:
    plugin:
      name: argocd-vault-plugin
```

