# Container Runtimes

```
apt-get update
apt install -y sudo
sudo apt-get install -y build-essential vim ansible python3-ansible-runner kmod
sudo apt-get update
sudo apt-get install -y apt-transport-https ca-certificates curl procps
sudo apt-get -y install bzip2 curl kmod qemu-utils

cat <<EOF | sudo tee /etc/modules-load.d/k8s.conf
overlay
br_netfilter
EOF

sudo modprobe overlay
sudo modprobe br_netfilter
```

# sysctl params required by setup, params persist across reboots

```
cat <<EOF | sudo tee /etc/modules-load.d/k8s.conf
overlay
br_netfilter
EOF

cat <<EOF | sudo tee /etc/sysctl.d/k8s.conf
net.bridge.bridge-nf-call-iptables  = 1
net.bridge.bridge-nf-call-ip6tables = 1
net.ipv4.ip_forward                 = 1
EOFsudo apt-get install -y kubelet kubeadm kubectl
```

# Apply sysctl params without reboot

```
sudo sysctl --system
```

```
lsmod | grep br_netfilter
lsmod | grep overlay

sysctl net.bridge.bridge-nf-call-iptables net.bridge.bridge-nf-call-ip6tables net.ipv4.ip_forward
```

#-------------

```
sudo apt-get update
mkdir /etc/apt/keyrings/
curl -fsSL https://dl.k8s.io/apt/doc/apt-key.gpg | sudo gpg --dearmor -o \
 /etc/apt/keyrings/kubernetes-archive-keyring.gpg
echo "deb [signed-by=/etc/apt/keyrings/kubernetes-archive-keyring.gpg] \
 https://apt.kubernetes.io/ kubernetes-xenial main" | sudo tee \
 /etc/apt/sources.list.d/kubernetes.list
sudo apt-get update
sudo apt-get install -y kubelet kubeadm kubectl
sudo apt-mark hold kubelet kubeadm kubectl
```
