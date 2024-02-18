### Section 7(Stacked ETCD)

How is ETCD configured for cluster1?

Remember, you can access the clusters from student-node using the kubectl tool. You can also ssh to the cluster nodes from the student-node.

Make sure to switch the context to cluster1:

```
student-node ~ ➜   kubectl config use-context cluster1
```

```
student-node ~ ➜   kubectl get pods -n kube-system | grep etcd
```

etcd-cluster1-controlplane 1/1 Running 0 59m

上記コマンド結果に etcd という pod があれば、stack 型 etcd を使用していることになり、etcd サーバーが controlplane ノード自体で動作していることを意味します。

別の手段として以下の通りとなる

```
student-node ~ ✖  kubectl get pods -n kube-system
NAME                                            READY   STATUS    RESTARTS      AGE
coredns-6d4b75cb6d-9tgls                        1/1     Running   0             69m
coredns-6d4b75cb6d-g4dgb                        1/1     Running   0             69m
etcd-cluster1-controlplane                      1/1     Running   0             69m
kube-apiserver-cluster1-controlplane            1/1     Running   0             69m
kube-controller-manager-cluster1-controlplane   1/1     Running   0             69m
kube-proxy-8c6wm                                1/1     Running   0             69m
kube-proxy-8nmnz                                1/1     Running   0             69m
kube-scheduler-cluster1-controlplane            1/1     Running   0             69m
weave-net-bqjsj                                 2/2     Running   1 (69m ago)   69m
weave-net-d8r22                                 2/2     Running   0             69m

student-node ~ ➜  k describe po kube-apiserver-cluster1-controlplane -n kube-system | grep etcd-server
      --etcd-servers=https://127.0.0.1:2379
```

### Section 10 (data-dir)

```
student-node ~ ✖ k -n kube-system describe pod etcd-cluster1-controlplane | grep data-dir
      --data-dir=/var/lib/etcd
```

### Section 12

```
student-node ~ ➜  ssh etcd-server


etcd-server ~ ➜  ps -ef | grep etcd
etcd         816       1  0 09:00 ?        00:01:03 /usr/local/bin/etcd --name etcd-server --data-dir=/var/lib/etcd-data --cert-file=/etc/etcd/pki/etcd.pem --key-file=/etc/etcd/pki/etcd-key.pem --peer-cert-file=/etc/etcd/pki/etcd.pem --peer-key-file=/etc/etcd/pki/etcd-key.pem --trusted-ca-file=/etc/etcd/pki/ca.pem --peer-trusted-ca-file=/etc/etcd/pki/ca.pem --peer-client-cert-auth --client-cert-auth --initial-advertise-peer-urls https://192.13.128.15:2380 --listen-peer-urls https://192.13.128.15:2380 --advertise-client-urls https://192.13.128.15:2379 --listen-client-urls https://192.13.128.15:2379,https://127.0.0.1:2379 --initial-cluster-token etcd-cluster-1 --initial-cluster etcd-server=https://192.13.128.15:2380 --initial-cluster-state new
```

### Section 13

    How many nodes are part of the ETCD cluster that etcd-server is a part of?

```
etcd-server ~ ✖ ETCDCTL_API=3 etcdctl \
   --endpoints=https://127.0.0.1:2379 \
   --cacert=/etc/etcd/pki/ca.pem \
   --cert=/etc/etcd/pki/etcd.pem \
   --key=/etc/etcd/pki/etcd-key.pem \
    member list
9fe44cbb135a8713, started, etcd-server, https://192.13.128.15:2380, https://192.13.128.15:2379, false
```

### Section 14

```
student-node ~ ➜  kubectl config use-context cluster1
Switched to context "cluster1".

student-node ~ ✖ kubectl describe  pods -n kube-system etcd-cluster1-controlplane  | grep advertise-client-urls
      --advertise-client-urls=https://10.1.218.16:2379

student-node ~ ➜  kubectl describe  pods -n kube-system etcd-cluster1-controlplane  | grep pki
      --cert-file=/etc/kubernetes/pki/etcd/server.crt
      --key-file=/etc/kubernetes/pki/etcd/server.key
      --peer-cert-file=/etc/kubernetes/pki/etcd/peer.crt
      --peer-key-file=/etc/kubernetes/pki/etcd/peer.key
      --peer-trusted-ca-file=/etc/kubernetes/pki/etcd/ca.crt
      --trusted-ca-file=/etc/kubernetes/pki/etcd/ca.crt
      /etc/kubernetes/pki/etcd from etcd-certs (rw)
    Path:          /etc/kubernetes/pki/etcd

student-node ~ ✖ k get nodes
NAME                    STATUS   ROLES           AGE    VERSION
cluster1-controlplane   Ready    control-plane   109m   v1.24.0
cluster1-node01         Ready    <none>          109m   v1.24.0

student-node ~ ➜  ssh cluster1-controlplane
Welcome to Ubuntu 18.04.6 LTS (GNU/Linux 5.4.0-1106-gcp x86_64)

cluster1-controlplane ~ ➜  ETCDCTL_API=3 etcdctl \
      --endpoints=https://10.1.220.8:2379 \
      --cacert=/etc/kubernetes/pki/etcd/ca.crt \
      --cert=/etc/kubernetes/pki/etcd/server.crt \
      --key=/etc/kubernetes/pki/etcd/server.key \
      snapshot save /opt/cluster1.db
Snapshot saved at /opt/cluster1.db

student-node ~ ➜  scp cluster1-controlplane:/opt/cluster1.db /opt/
cluster1.db                                              100% 2040KB 151.9MB/s   00:00

```

```
ETCDCTL_API=3 etcdctl --endpoints=https://[127.0.0.1]:2379 --cacert=/etc/kubernetes/pki/etcd/ca.crt \
     --cert=/etc/kubernetes/pki/etcd/server.crt --key=/etc/kubernetes/pki/etcd/server.key \
     snapshot save /path/to/file.db
```

### Section 15

```
student-node ~ ➜  kubectl config use-context cluster2
Switched to context "cluster2".

student-node ~ ➜  scp /opt/cluster2.db  etcd-server:/opt/
cluster2.db                                              100% 2016KB 130.5MB/s   00:00

student-node ~ ➜  ssh etcd-server
Welcome to Ubuntu 18.04.6 LTS (GNU/Linux 5.4.0-1106-gcp x86_64)

etcd-server ~ ✖ ETCDCTL_API=3 etcdctl \
    snapshot restore /opt/cluster2.db \
    --data-dir /var/lib/etcd-data-new

etcd-server ~ ➜  sudo chown -R etcd:etcd /var/lib/etcd-data-new/

etcd-server ~ ➜  vi /etc/systemd/system/etcd.service

etcd-server ~ ➜  systemctl daemon-reload

etcd-server ~ ➜  systemctl restart etcd

etcd-server ~ ➜  systemctl status etcd

student-node ~ ➜  k delete po kube-controller-manager-cluster2-controlplane kube-scheduler-cluster2-controlplane
```
