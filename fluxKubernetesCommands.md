Bootstrap a flux repo that will automatically track our updates
flux bootstrap gitlab \
  --deploy-token-auth \
  --owner=joshuamclapp \
  --repository=minisform-kuber-cluster \
  --branch=master \
  --path=clusters/my-cluster \
  --personal


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