```
k describe pod etcd-controleplane -n kube-system
```

```
ETCDCTL_API=3 etcdctl --data-dir /var/lib/etcd-from-backup \
  snapshot restore  /opt/snapshot-pre-boot.db
```

etcd.yaml

```
volume セクションの path にある/var/lib/etcd を/var/lib/etcd-from-backup へ
```

etcd-controlplane が起動してこない場合は pod を削除して際作成されるのを待

```
k delete etcd-controlplane -n kube-system
```
