Bootstrap a flux repo that will automatically track our updates
flux bootstrap gitlab \
  --deploy-token-auth \
  --owner=joshuamclapp \
  --repository=minisform-kuber-cluster \
  --branch=master \
  --path=clusters/my-cluster \
  --personal

flux bootstrap gitlab \
  --deploy-token-auth \
  --owner=joshuamclapp \
  --repository=minecraft-cluster-gitops\
  --branch=master \
  --path=clusters \
  --personal

# create a flux SSH secret, the PAT give access to all repos

flux create secret git flux-system --url=https://git@gitlab.com/minisform-kuber-cluster.git --password=<given token> --username=joshuamclapp

https://fluxcd.io/flux/cmd/flux_create_secret_git/

We are going to be using flux to automatically deploy gitlab
# Check Flux components
kubectl get pods -n flux-system

# Check Flux status
flux check

Now if we update nginx replicas, flux will automatically redeploy our instances and kuber:

# See live flux deploy logs from git
kubectl get gitrepository flux-system -n flux-system -o yaml

# Force immediate sync (don't wait for interval)
flux reconcile source git flux-system

# Check what Flux is managing
flux get all

# View Flux logs
flux logs --all-namespaces --follow --tail=10

# See what changed in last sync
flux diff source git flux-system

# Suspend auto-sync (for debugging)
flux suspend kustomization flux-system

# Resume auto-sync
flux resume kustomization flux-system


