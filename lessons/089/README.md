# GitHub Actions Self Hosted Runner (Autoscaling with Kubernetes)

[YouTube Tutorial]()

Points
- Free
- No incoming connections only long poll
- Many people already have Kubernetes

- Are free to use with GitHub Actions, but you are responsible for the cost of maintaining your runner machines.
- Since the self-hosted runner opens a connection to GitHub, you do not need to allow GitHub to make inbound connections to your self-hosted runner.
- We recommend that you only use self-hosted runners with private repositories.

## Content

## Create GitHub Repository

- Create private GitHub repository `lesson-089`


## Create EKS Cluster with Terraform

- Go over each file under `terraform` folder

- Create EKS with terraform
```bash
terraform init
terraform apply
```

- Configure kubectl
```bash
aws eks --region us-east-1 update-kubeconfig --name demo
```
if you get an error `'NoneType' object is not iterable`, run `rm ~/.kube/config`

- Check connection with Kubernetes
```bash
kubectl get svc
```
## Install Cert-Manager on Kubernetes

- Add Helm repo
```bash
helm repo add jetstack https://charts.jetstack.io
```

- Update Helm
```bash
helm repo update
```

- Search for latest verion
```bash
helm repo search cert-manager 
```

- Install Helm chart
```bash
helm install \
  cert-manager jetstack/cert-manager \
  --namespace cert-manager \
  --create-namespace \
  --version v1.6.0 \
  --set prometheus.enabled=false \
  --set installCRDs=true
```

## Links

- [Hosting your own runners](https://docs.github.com/en/actions/hosting-your-own-runners)
- [GitHub Actions Runner](https://github.com/actions/runner)
- [actions-runner-controller](https://github.com/actions-runner-controller/actions-runner-controller)




values.yaml https://github.com/actions-runner-controller/actions-runner-controller/blob/master/charts/actions-runner-controller/values.yaml


App ID: 148756
https://github.com/settings/installations/20434974

kubectl create ns actions-runner-system

kubectl create secret generic controller-manager \
    -n actions-runner-system \
    --from-literal=github_app_id=148756 \
    --from-literal=github_app_installation_id=20434974 \
    --from-file=github_app_private_key=lesson-089.2021-11-01.private-key.pem

helm repo add actions-runner-controller https://actions-runner-controller.github.io/actions-runner-controller
helm repo update

helm install actions-runner-system \
    actions-runner-controller/actions-runner-controller \
    --namespace actions-runner-system \
    --version 0.14.0 \
    --set syncPeriod=1m
