# coit-frontend-devsecops: Secured Frontend Deployment

The React sentiment-analysis frontend ([coit-frontend](https://github.com/nixvarghese01/coit-frontend)) with a DevSecOps focus: a hardened Dockerfile, Kustomize overlays per environment, and **Kyverno** admission policies for the cluster.

## Repository layout
```
coit-frontend/             React app
  Dockerfile-Secure        hardened image
  Dockerfile-multistage    Node build, then nginx
frontend-kustomize-stage/  Stage overlay: deployment, service, ingress, HPA, RBAC, cert-manager issuers, config maps
frontend-kustomize-prod/   Prod overlay (same resources, prod values)
kyverno/                   Cluster policies
  validate/                require names and namespace labels
  mutate/                  modify ConfigMaps, set imagePullPolicy, remove fields
  generate/                create a ConfigMap in new namespaces
  images/                  pod image and signature checks
  policy/                  restrict image registries, namespace rules
  useSignedImages.yaml     only allow signed images
```

## Usage
```bash
# build the image
docker build -f coit-frontend/Dockerfile-Secure -t <dockerhub-user>/coit-frontend coit-frontend

# install Kyverno, then apply the policies
kubectl create -f https://github.com/kyverno/kyverno/releases/latest/download/install.yaml
kubectl apply -f kyverno/ --recursive

# deploy
kubectl apply -k frontend-kustomize-stage
```

> `config.properties` and `provider.properties` contain placeholder values only. Use Kubernetes Secrets for real credentials.

## Branches
`dev` (default)
