brew install fluxcd/tap/flux
flux --version

<!-- Read access to metadata
 Read and Write access to administration and contents -->
export GITHUB_TOKEN=
export GITHUB_USER=petercorrea
export REPO=gitops

flux bootstrap github \
  --owner=$GITHUB_USER \
  --repository=$REPO \
  --branch=main \
  --path=clusters/flux-demo \
  --personal

flux check
flux get kustomizations -A
kubectl -n flux-system get pods

watch kubectl get nodes,pods --all-namespaces

kubectl -n default port-forward svc/podinfo 8080:80
