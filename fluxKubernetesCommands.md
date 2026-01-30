Bootstrap a flux repo that will automatically track our updates
flux bootstrap gitlab \
  --deploy-token-auth \
  --owner=joshuamclapp \
  --repository=minisform-kuber-cluster \
  --branch=master \
  --path=clusters/my-cluster \
  --personal

# create a flux SSH secret
flux create secret git flux-system --url=git@gitlab.com/minisform-kuber-cluster.git --ssh-key-algorithm=ecdsa --ssh-ecdsa-curve=p521

https://fluxcd.io/flux/cmd/flux_create_secret_git/
https://fluxcd.io/flux/cmd/flux_create_secret_git/

We are going to be using flux to automatically deploy gitlab
# Check Flux components
kubectl get pods -n flux-system

# Check Flux status
flux check

Now if we update nginx replicas, flux will automatically redeploy our instances and kuber:

# Edit the deployment file
sed -i 's/replicas: 2/replicas: 3/' clusters/my-cluster/apps/nginx/deployment.yaml

# Commit and push
git add .
git commit -m "Scale nginx to 3 replicas"
git push

# Watch Flux automatically scale your deployment
kubectl get deployments nginx -w

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


