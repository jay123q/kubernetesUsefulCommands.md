To login:
ssh jclapp@192.168.1.112

You have disabled rootlogin in SSH by disabling 'PermitRootLogin' in /etc/ssh/sshd_config, and other users from adding keys to log in by disabling 'PasswordAuthentication' in the same file. This means that only already athenticated users can log in.


sudo kubeadm reset -f
-> kill all processes currently running on kubernentes

sudo kubeadm init
-> initialize kubernentes

rm -rf ~/.kube/cache
-> remove cache to handle errant weirder errors


INSTALL CNI
CILIUM_CLI_VERSION=$(curl -s https://raw.githubusercontent.com/cilium/cilium-cli/main/stable.txt)
GOOS=$(go env GOOS)
GOARCH=$(go env GOARCH)
curl -L --remote-name-all https://github.com/cilium/cilium-cli/releases/download/${CILIUM_CLI_VERSION}/cilium-$>
sudo tar -C /usr/local/bin -xzvf cilium-${GOOS}-${GOARCH}.tar.gz
rm cilium-${GOOS}-${GOARCH}.tar.gz

# To view the web UI
cilium hubble ui
-> this provides deep packet visibility

kubectl get pods -A
-> see all pods across all namespaces

kubectl logs -n kube-flannel kube-flannel-ds-v99qc
-> see individual pod logs

kubectl get pods -o wide
-> get all pods ips and what node it is running on

kubectl get pods -n <namespace to inspect>

if you are seeing that the controlplane node in 
kubectl get node
-> Status is showing NotReady
You can fix this by using 'sudo systemctl restart kubelet' if it is a cilium networking issue
