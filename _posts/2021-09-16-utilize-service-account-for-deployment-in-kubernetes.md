---
title: Utilize Service Account for Deployment in kubernetes
tags:
- kubernetes
---

Recently kubernetes team in my company decided to ban user to use cluster-admin / admin account in kubernetes, so I had to find new way to authenticate in deployment step. <br>
Shortly, I created new service account in namespace with edit role, and replace previous token with newly-created account's token. 
## Create Service Account
Change workspace if necessary
```bash
kubectl config set-context --current --namespace=<namespace>
```
<br>
create yaml file to create service account
```yaml
apiVersion: v1
kind: ServiceAccount
metadata:
    name: <account-name>
```
<br>
apply it. Note that service account binds to namespace (except for cluster-admin role)
```bash
kubectl apply -f <filename>
```

## Bind Role
There are four service account roles: cluster-admin / admin / edit / view. <br>
service account with cluster-admin / admin / edit role can be utilized in deployment step. <br>
So I created edit role binding:
```bash
kubectl create rolebinding <role> --clusterrole=edit --serviceaccount=<namespace>:<service-account>
```
## Write Kubeconfig yaml
Find secret token from the service account.<br>
First find secret name:
```bash
kubectl describe serviceAccount <service-account>
 
// result
Name:                <account>
Namespace:           <namespace>
Labels:              <none>
Annotations:         <none>
Image pull secrets:  <none>
Mountable secrets:   <secret>
Tokens:              <secret> // this one
Events:              <none>
```

find token using secret name. Note that there would be no expiration as I didn't set expiration time above.
```bash
kubectl describe secret <secret>
 
// result
Data
====
ca.crt:     ...
namespace:  ...
token:      <token> // this one
```

write kubeconfig yaml using that token. Usually you can find existing config in ~/.kube/config
```yaml
apiVersion: v1
clusters:
  - cluster:
      server: <cluster-server>
    name: <cluster>
contexts:
  - context:
      cluster: <cluster>
      namespace: <namespace>
      user: <service-account> // this
    name: <cluster>
current-context: <context>
kind: Config
preferences: {}
users:
  - name: <service-account> // this
    user:
      token: <token> // this
```

add service account in deployment yaml
```yaml
apiVersion: apps/v1
kind: Deployment
...
spec:
  ...
  selector:
     ...
  template:
    ...
    spec:
      serviceAccountName: <service-account> // this
      automountServiceAccountToken: true // this
      containers:
        ...
```

## References

[https://kubernetes.io/docs/tasks/configure-pod-container/configure-service-account/](https://kubernetes.io/docs/tasks/configure-pod-container/configure-service-account/) <br>
[http://docs.shippable.com/deploy/tutorial/create-kubeconfig-for-self-hosted-kubernetes-cluster/](http://docs.shippable.com/deploy/tutorial/create-kubeconfig-for-self-hosted-kubernetes-cluster/)<br>
[https://kubernetes.io/docs/reference/access-authn-authz/rbac/](https://kubernetes.io/docs/reference/access-authn-authz/rbac/) <br>
[https://kubernetes.io/docs/concepts/policy/pod-security-policy/](https://kubernetes.io/docs/concepts/policy/pod-security-policy/)
