# gitops-terraform-fluxcd-workflow

This model provides a comprehensive approach to GitOps, encompassing everything from provisioning to enforcement. For instance, it could manage the entire lifecycle of an AKS/EKS cluster via GitOps, automating numerous underlying processes.

## Set up

```
kind create cluster --name flux-cluster

curl -s https://fluxcd.io/install.sh | sudo bash
export GITHUB_TOKEN=[GITHUB_PAT_TOKEN]
      --token-auth \
      --owner=[GITHUB_ACCOUNT] \
      --repository=gitops-terraform-workflow \
      --branch=main \
      --path=clusters/local \
      --personal

kubectl apply -f https://raw.githubusercontent.com/flux-iac/tofu-controller/main/docs/release.yaml
```

## Configure Secret

We are running Terraform in a Kubernetes cluster using Flux to automate Terraform operations, so we need to use Kubernetes Secrets to store Azure credentials and the PAT. These secrets are then mounted into your Pod where Terraform executes.

```
kubectl apply -f github-repository-secret.yaml

kubectl create secret generic azure-creds \
    --from-literal=ARM_SUBSCRIPTION_ID='SP_SUB_ID' \
    --from-literal=ARM_TENANT_ID='SP_TENANT_ID' \
    --from-literal=ARM_CLIENT_ID='SP_APPID' \
    --from-literal=ARM_CLIENT_SECRET='SP_PASSWORD' \
    --namespace flux-system

kubectl apply -f github-repository-definition.yaml

docker push savagame/gitops-terraform:tf_az_cli_1_1

```
