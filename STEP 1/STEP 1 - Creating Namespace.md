We are creating this namespace so that we can setup are logging requirements and manage installations. Objects running in your Kubernetes cluster needs to be used separately which is achieved by this namespace. In our kubernetes system we have some of the pre-installed namespaces, view them by:-

```
kubectl get namespaces
```

Apart from the default, kube-system and kube-public namespaces we are going to make one for our own use by making a new file kube-logs.yaml:-

```
nano kube-logs.yaml
```

```
kind: Namespace
apiVersion: v1
metadata:
  name: kube-logs
```

Once the file is saved this object file is needed to be applied:-

```
kubectl create -f kube-logs.yaml    #You shall receive a namespace created message
```
