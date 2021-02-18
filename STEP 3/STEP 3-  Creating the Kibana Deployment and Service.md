For making use of Kibana in Kubernetes we shall make its service and deployment requirements in a file named kibana.yaml

```
apiVersion: v1
kind: Service						#Kind of object is service
metadata:
  name: kibana
  namespace: kube-logs
  labels:
    app: kibana
spec:
  ports:
  - port: 5601					         #To access kibana we shall mention port
  selector:
    app: kibana
---
apiVersion: apps/v1
kind: Deployment				                      #Now for Deployment
metadata:
  name: kibana
  namespace: kube-logs
  labels:
    app: kibana
spec:
  replicas: 1						         #Pod needed is 1
  selector:
    matchLabels:
      app: kibana
  template:
    metadata:
      labels:
        app: kibana
    spec:
      containers:
      - name: kibana
        image: docker.elastic.co/kibana/kibana:7.2.0 	#Tag of image can be of choice
        resources:
          limits:
            cpu: 1000m
          requests:
            cpu: 100m
        env:
          - name: ELASTICSEARCH_URL		#This environment variable sets the 
					                  #endpoint and port for Elasticsearch cluster
            value: http://elasticsearch:9200
        ports:
        - containerPort: 5601
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
