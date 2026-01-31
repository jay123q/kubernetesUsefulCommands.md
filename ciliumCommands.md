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


curl http://192.168.1.112:32046 
-> works this uses node port via metallb


curl http://192.168.1.112:32046
  # Or open in your browser!   

https://docs.cilium.io/en/stable/gettingstarted/k8s-install-default/