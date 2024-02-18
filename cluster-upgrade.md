# On the controlplane node:

```
export KUBEADM_VER=1.27.0
export KUBELET_VER=1.27.0

kubectl drain controlplane --ignore-daemonsets
apt update
apt-get install  -y kubeadm=${KUBEADM_VER}-00
kubeadm upgrade plan v${KUBEADM_VER}
kubeadm upgrade apply v${KUBEADM_VER}
apt-get install  -y kubelet=${KUBELET_VER}-00
systemctl daemon-reload
systemctl restart kubelet
kubectl uncordon controlplane
```

# Before draining node01, we need to remove the taint from the controlplane node.

### Identify the taint first.

```
kubectl describe node controlplane | grep -i taint
```

### Remove the taint with help of "kubectl taint" command.

```
kubectl taint node controlplane node-role.kubernetes.io/control-plane:NoSchedule-
```

### Verify it, the taint has been removed successfully.

```
kubectl describe node controlplane | grep -i taint
```

### Now, drain the node01 as follows: on controlplane

```
kubectl drain node01 --ignore-daemonsets
```

# On The node01 Node.

### SSH to the node01 and perform the below steps as follows: -

```
export KUBEADM_VER=1.27.0
export KUBELET_VER=1.27.0
apt update
apt-get install kubeadm=${KUBEADM_VER}-00
kubeadm upgrade node
apt-get install kubelet=${KUBELET_VER}-00
systemctl daemon-reload
systemctl restart kubelet
```

### To exit from the specific node, type exit or logout on the terminal.

### Back on the controlplane node: -

```
kubectl uncordon node01
kubectl get pods -o wide | grep gold (make sure this is scheduled on a node)
```
