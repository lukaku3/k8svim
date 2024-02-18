# Taint the Worker Node

```
kubectl taint nodes node01 key=value:NoSchedule
```

# Create a Pod dev-redis

```
cat > dev-redis.yaml << EOF
apiVersion: v1
kind: Pod
metadata:
  name: dev-redis
spec:
  containers:
  - name: redis
    image: redis:alpine

EOF
```

```
kubectl apply -f dev-redis.yaml
```

```
cat > dev-redis.yaml << EOF
apiVersion: v1
kind: Pod
metadata:
  name: prod-redis
spec:
  containers:
  - name: redis
    image: redis:alpine
  tolerations:
  - key: "env_key"
    operator: "Equal"
    value: "production"
    effect: "NoSchedule"

EOF
```

```
kubectl apply -f prod-redis.yaml
```
