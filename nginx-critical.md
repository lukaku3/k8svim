To create a static pod called nginx-critical by using below command:

```
kubectl run nginx-critical --image=nginx --dry-run=client -o yaml > static.yaml
```

Copy the contents of this file or use scp command to transfer this file from controlplane to node01 node.

```
root@controlplane:~# scp static.yaml node01:/root/
```

To know the IP Address of the node01 node:

```
root@controlplane:~# kubectl get nodes -o wide
```

# Perform SSH

```
root@controlplane:~# ssh node01
OR
root@controlplane:~# ssh <IP of node01>
On node01 node:
```

Check if static pod directory is present which is /etc/kubernetes/manifests, if it's not present then create it.

```
root@node01:~# mkdir -p /etc/kubernetes/manifests
```

Add that complete path to the staticPodPath field in the kubelet config.yaml file.

```
root@node01:~# vi /var/lib/kubelet/config.yaml
```

now, move/copy the static.yaml to path /etc/kubernetes/manifests/.

```
root@node01:~# cp /root/static.yaml /etc/kubernetes/manifests/
```

Go back to the controlplane node and check the status of static pod:

```
root@node01:~# exit
logout
root@controlplane:~# kubectl get pods
```
