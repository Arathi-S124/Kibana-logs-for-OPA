For making use of Kibana in Kubernetes we shall make its service and deployment requirements in a file named kibana.yaml

```
nano kibana.yaml
```

Save the file and now we can roll out and deploy our Kibana:-

```
kubectl create -f kibana.yaml
```
Again for checking the successful run use:-

```
kubectl rollout status deployment/kibana --namespace=kube-logs
```
We shall now fetch the Kibana pod details and move ahead to forward its local port 5601 to port 5601.

```
kubectl get pods --namespace=kube-logs
```
```
kubectl port-forward kibana-<your-unique-pod>:5601 --namespace=kube-logs
```
Now access Kibana on web browser:-

```
http://localhost:5601
```

![Kibana](https://user-images.githubusercontent.com/62760235/108327188-1a771180-71f1-11eb-8735-c9ebfb87d0bb.png)
