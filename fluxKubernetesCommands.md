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

# Flux secrets
═══════════════════════════════════════════════════════════════════
  FLUX SYNC FIX - FINAL SOLUTION
═══════════════════════════════════════════════════════════════════

The reconciliation timed out because I switched minecraft-gitops to HTTPS,
but the repository is private. Let me give you the correct fix:

═══════════════════════════════════════════════════════════════════
STEP 1: Fix minecraft-gitops (revert back to SSH)
═══════════════════════════════════════════════════════════════════

kubectl patch gitrepository minecraft-gitops -n flux-system --type=json \
  -p='[{"op": "replace", "path": "/spec/url", "value": "ssh://git@github.com/jay123q/minecraft-cluster-gitops.git"}]'

kubectl patch gitrepository minecraft-gitops -n flux-system --type=json \
  -p='[{"op": "add", "path": "/spec/secretRef", "value": {"name": "flux-system"}}]'

═══════════════════════════════════════════════════════════════════
STEP 2: Get BOTH deploy keys
═══════════════════════════════════════════════════════════════════

# Deploy key for minecraft-cluster-gitops:
kubectl get secret flux-system -n flux-system -o jsonpath='{.data.identity\.pub}' | base64 -d
ecdsa-sha2-nistp384 AAAAE2VjZHNhLXNoYTItbmlzdHAzODQAAAAIbmlzdHAzODQAAABhBLs3YyJY1GbotA25t6ImjLoqWstbgxyNYRShgp9xYrs4GUHmU3+ogrfsmsz/dsGo7wO1TL0pipVX5QmDDrcC7o9Oo3EBVgCZ/tpIcDaaBatRJQct+T9HKi5JOdI/uhtn3g==

# Deploy key for minecraft_CoR:
kubectl get secret minecraft-cor -n flux-system -o jsonpath='{.data.identity\.pub}' | base64 -d
ecdsa-sha2-nistp384 AAAAE2VjZHNhLXNoYTItbmlzdHAzODQAAAAIbmlzdHAzODQAAABhBB1me3zVgWNVahZPtMudqEQILkYphpIhnqtyPt8Net9bgADV1ReVEyXugiCANc+FscPHdKc/pD9HLjVgM4dLH7Gk9Y3RoRPN4MRicX5B5aiyWXSKLK8Zc/ApBCgQV3/QNg==
═══════════════════════════════════════════════════════════════════
STEP 3: Add BOTH deploy keys to GitHub
═══════════════════════════════════════════════════════════════════

For minecraft-cluster-gitops:
  1. Copy the first key from Step 2
  2. Go to: https://github.com/jay123q/minecraft-cluster-gitops/settings/keys
  3. Click "Add deploy key"
  4. Title: flux-minecraft-gitops
  5. Paste the key
  6. DO NOT check "Allow write access"
  7. Click "Add key"

For minecraft_CoR:
  1. Copy the second key from Step 2
  2. Go to: https://github.com/jay123q/minecraft_CoR/settings/keys
  3. Click "Add deploy key"
  4. Title: flux-minecraft-cor
  5. Paste the key
  6. DO NOT check "Allow write access"
  7. Click "Add key"

═══════════════════════════════════════════════════════════════════
STEP 4: (Optional) Clean up flux-system GitRepository
═══════════════════════════════════════════════════════════════════

kubectl delete gitrepository flux-system -n flux-system

This is safe - Flux will continue working from local bootstrap files.

═══════════════════════════════════════════════════════════════════
STEP 5: Reconcile and verify
═══════════════════════════════════════════════════════════════════

# Force reconciliation
flux reconcile source git minecraft-gitops -n flux-system
flux reconcile source git minecraft-cor -n flux-system

# Verify all sources are ready
flux get sources git -A

# Check your new Minecraft server
kubectl get all -n minecraft-cor

═══════════════════════════════════════════════════════════════════
EXPECTED RESULT
═══════════════════════════════════════════════════════════════════

flux-system        → Can be deleted (optional)
minecraft-gitops   → READY=True ✓
minecraft-cor      → READY=True ✓

minecraft-cor namespace will be created
minecraft-cor-server pod will be deployed
Server available at: 192.168.1.202:25565

═══════════════════════════════════════════════════════════════════
QUICK COPY-PASTE COMMANDS
═══════════════════════════════════════════════════════════════════

# Fix minecraft-gitops URL
kubectl patch gitrepository minecraft-gitops -n flux-system --type=json -p='[{"op": "replace", "path": "/spec/url", "value": "ssh://git@github.com/jay123q/minecraft-cluster-gitops.git"}]'
kubectl patch gitrepository minecraft-gitops -n flux-system --type=json -p='[{"op": "add", "path": "/spec/secretRef", "value": {"name": "flux-system"}}]'

# Get deploy keys
echo ""
echo "══════ MINECRAFT-GITOPS DEPLOY KEY ══════"
kubectl get secret flux-system -n flux-system -o jsonpath='{.data.identity\.pub}' | base64 -d
echo ""
echo "Add to: https://github.com/jay123q/minecraft-cluster-gitops/settings/keys"
echo ""
echo "══════ MINECRAFT-COR DEPLOY KEY ══════"
kubectl get secret minecraft-cor -n flux-system -o jsonpath='{.data.identity\.pub}' | base64 -d
echo ""
echo "Add to: https://github.com/jay123q/minecraft_CoR/settings/keys"
echo ""

# After adding keys, reconcile
flux reconcile source git minecraft-gitops -n flux-system
flux reconcile source git minecraft-cor -n flux-system

# Verify
flux get sources git -A
kubectl get all -n minecraft-cor

═══════════════════════════════════════════════════════════════════
flux reconcile source git minecraft-cor -n flux-system

# Generate a new flux key

ssh-keygen -t ed25519 -f ~/.ssh/id_ed25519_minecraft_cor -C "joshuamclapp@gmail.com"
-> generate a new key in -f dir

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
`
# Resume auto-sync`
flux resume kustomization flux-system


