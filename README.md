# GitOps

GitOps = Git is the single source of truth

Flux is a GitOps controller for Kubernetes. 
It continuously reconciles cluster state from Git, making deployments auditable, secure, and reproducible. 
Instead of pushing changes into clusters, clusters pull from Git, reducing risk and drift while improving reliability and recovery.

With Flux, the desired state of the cluster lives in Git. 
The cluster continuously reconciles itself to match Git and corrects drift.

Why this matters:
- No more mystery state or “who ran kubectl last”
- Audit log = Git history 
- Rollbacks are as simple as git revert
- Environment parity and repeatability
- Native Kubernetes, no proprietary abstractions
- GitOps enables infrastructure immutability: If a cluster dies, we don’t restore from backups, instead we redeploy from Git;

## Security
Flux runs inside the cluster and pulls from Git — nothing needs cluster credentials outside.

Why it matters:
- CI systems don’t need admin access to clusters
- No inbound firewall holes
- Works cleanly with private clusters

## Setup
1. Install flux locally
```shell
brew install fluxcd/tap/flux
flux --version
```
2. Create a GH access token with the following permissions:
- Read access to metadata
- Read and Write access to administration and contents

3. Export environment variables
```shell
export GITHUB_TOKEN=
export GITHUB_USER=petercorrea
export REPO=gitops
```

4. Bootstrap flux into the cluster
```shell 
flux bootstrap github \
  --owner=$GITHUB_USER \
  --repository=$REPO \
  --branch=main \
  --path=clusters/flux-demo \
  --personal

flux check
flux get kustomizations -A
kubectl -n flux-system get pods
watch kubectl get nodes,service,pods --all-namespaces -o wide
```

5. Commit K8s changes and observe!
- `kubectl apply` was never used

## Demo
```shell
kubectl -n default port-forward svc/podinfo 8080:80
# http://localhost:8080/
kubectl delete deployment podinfo
```
