### Use the command kubectl run and create a nginx pod and busybox pod. Resolve it, nginx service and its pod name from busybox pod.

To create a pod nginx-resolver and expose it internally:

```
kubectl run nginx-resolver --image=nginx
kubectl expose pod nginx-resolver --name=nginx-resolver-service --port=80 --target-port=80 --type=ClusterIP
```

To create a pod test-nslookup. Test that you are able to look up the service and pod names from within the cluster:

```
kubectl run test-nslookup --image=busybox:1.28 --rm -it --restart=Never -- nslookup nginx-resolver-service
```

Get the IP of the nginx-resolver pod and replace the dots(.) with hyphon(-) which will be used below.

```
kubectl get pod nginx-resolver -o wide
kubectl run test-nslookup --image=busybox:1.28 --rm -it --restart=Never -- nslookup <P-O-D-I-P.default.pod> > /root/CKA/nginx.pod
```

```
---
apiVersion: v1
kind: Pod
metadata:
  creationTimestamp: null
  labels:
    run: nginx-resolver
  name: nginx-resolver
spec:
  containers:
    - image: nginx
      name: nginx-resolver
      resources: {}
  dnsPolicy: ClusterFirst
  restartPolicy: Always
status: {}
---
apiVersion: v1
kind: Service
metadata:
  name: nginx-resolver-service
  namespace: default
spec:
  selector:
    app: nginx-resolver-service
  type: ClusterIP
  ports:
    - name: nginx-resolver-service
      protocol: TCP
      port: 80
      targetPort: 80
      # If you set the `spec.type` field to `NodePort` and you want a specific port number,
      # you can specify a value in the `spec.ports[*].nodePort` field.
      #nodePort:
```
