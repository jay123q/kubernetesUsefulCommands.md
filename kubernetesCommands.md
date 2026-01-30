To login:
ssh jclapp@192.168.1.112

You have disabled rootlogin in SSH by disabling 'PermitRootLogin' in /etc/ssh/sshd_config, and other users from adding keys to log in by disabling 'PasswordAuthentication' in the same file. This means that only already athenticated users can log in.


sudo kubeadm reset -f
-> kill all processes currently running on kubernentes

sudo kubeadm init
-> initialize kubernentes

rm -rf ~/.kube/cache
-> remove cache to handle errant weirder errors

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

kubectl describe pod <pod-name>
-> find what is stopping a pod

kubectl delete pod foo --force
https://kubernetes.io/docs/reference/kubectl/generated/kubectl_delete/

kubectl delete pod argocd -n argocd-application-controller-1 --grace-period=0 --force

kubectl describe pod argocd-server-64dfb9989b-wj5wc -n argocd | tail -20

kubectl get nodes -o custom-columns=NAME:.metadata.name,TAINTS:.spec.taints

kubectl get configmap cilium-config -n kube-system -o yaml
-> generate a yaml output that has the output

kubectl get pods -n kube-system -l k8s-app=cilium --sort-by='.status.containerStatuses[0].restartCount'
-> search by all apps in the kube-system namespace and look for cilium with the status 0

kubectl get ipaddresspools -A && kubectl get l2advertisements -A
-> who is advertising on L2


Any service with type: LoadBalancer gets a MetalLB IP                                 
kubectl create service loadbalancer my-app --tcp=80:8080                                
                                                                                          
MetalLB automatically assigns an IP from the pool                                     
You can then access it at 192.168.1.20X                                               
curl http://192.168.1.200  # Returns your application                                   
                                                                                          
The flux-system reconciliation will eventually complete once all resources stabilize.   
The core functionality is working perfectly!