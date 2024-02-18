```
k get clusterrolebinding --no-headers | wc -l
```

```
k get clusterrolebinding | grep cluster-admin
```

```
k describe clusterrolebinding cluster-admin
```

```
k describe clusterrole cluster-admin
```

```
k create clusterrole michelle-role --verb=get,list,watch --resource=nodes
k create clusterolebinding michelle-role-binding --clusterrole=michelle-role --user=michelle
```

```
---
apiVersion: certificates.k8s.io/v1
kind: CertificateSigningRequest
metadata:
  name: john-developer
spec:
  signerName: kubernetes.io/kube-apiserver-client
  request: LS0tLS1CRUdJTiBDRVJUSUZJQ0FURSBSRVFVRVNULS0tLS0KTUlJQ1ZEQ0NBVHdDQVFBd0R6RU5NQXNHQTFVRUF3d0VhbTlvYmpDQ0FTSXdEUVlKS29aSWh2Y05BUUVCQlFBRApnZ0VQQURDQ0FRb0NnZ0VCQU9QUjBpYmkzb3pERFFLNXJYV0NaR1BpQmhuS2IvQUdFSEFPKzU2SnJjeVNzV1ZnCnlVd2k1ckNwNUcyemI4bG0wRWwrV2tKbHJlT3l3RVhnWmxJdGFaT1V4ckpvWlZTMnpKdzIrV2dBYkNYbXB6aHUKVThPNkhhRnVkR3VYaUQwTlRYbjQ2VmtiVXJXcDYyK0tHUzZnZUg2bENxbTd5VCtsZXVXRjgwaFRMTWFpL3ZTVwpkWjIyVlhCVm1nQzBvelhMNktEZFFXWXpnR283UmdteDRKcFVNM0k5Y1lMSlZMSVEwKzdpQmVYb0ViTndla1NSCmpleXpZYU5QNzJsb1A0U3dHcVg3Q0VqalVQT0l4UGk1NEFJTXFFR2FDWGhKSHc3bnk2Y3dKQjgxRHozMlVlaCsKbVZTVEF4VWpUN1dkMGdJRXRSTDFDL2hWMHRLSkF4TDNiblFrU1RFQ0F3RUFBYUFBTUEwR0NTcUdTSWIzRFFFQgpDd1VBQTRJQkFRRFNKc3RhNC8xQ212azA5RnZDTDkreW1YUGJQVC9QTzdJNng5Nkhlci9iaHZWWU12YTNYTnNDCkxoR2F0Y3JIZ2tKVExYWk14UVVyb1IwaXVoRnowRWx4MkxLTVpCUmJTOTJ4S04xTTcyM29YbE5RNWZ1ZXFIeXAKWUV3N0lQcHg2RDFwOGdEV0duU3lnSTQwcHF2Mk1leWJzTUd0aDlpT09Oc3lYbjFVMWpldkJZb1E2M3VTQngrKwozY3ZLWEUyZ1NRRHhvclllSVR0bmNnTjhMakFjS2hsQWRqSjZJWTRBZTc3c3pjdnR0S2UwdVFlMTNYczZvaWxSCkhMcmZoSkJwL3hsOExJL2lsVlhVSGhJOXM4UVlBZ0hkNmdpUkpseTdEYjFiZzg0ZFJBc0pic0kxNkUzUHZTMVQKd2U1MlhJNlFCVlRoRnVQRDlEcVhydG0vcFZGYTVmYWwKLS0tLS1FTkQgQ0VSVElGSUNBVEUgUkVRVUVTVC0tLS0tCg==
  usages:
  - digital signature
  - key encipherment
  - client auth

$ cat CKA/john.csr | base64 | tr -d "\n"
$ kubectl certificate approve john-developer
```

```
$ kubectl create role developer --resource=pods --verb=create,list,get,update,delete --namespace=development

$ kubectl create rolebinding developer-role-binding --role=developer --user=john --namespace=development

$ kubectl auth can-i update pods --as=john --namespace=development
```

# Other

### ServiceAccount

```
k create sa pvviewer
```

### ClusterRole

```
kubectl create clusterrole pvviewer-role --resource=persistentvolumes --verb=list
```

### CluserRoleBinding

```
kubectl create clusterrolebinding pvviewer-role-binding --clusterrole=pvviewer-role --serviceaccount=default:pvviewer
```

### Pod

```
---
apiVersion: v1
kind: Pod
metadata:
  labels:
    run: pvviewer
  name: pvviewer
spec:
  containers:
  - image: redis
    name: pvviewer
  # Add service account name
  serviceAccountName: pvviewer
```
