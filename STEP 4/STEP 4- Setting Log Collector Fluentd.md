Using Fluentd DaemonSet controller, we’ll roll out a Fluentd logging agent Pod on every node in our cluster. 

We shall move on to making fluentd.yaml file:-

```
apiVersion: v1
kind: ServiceAccount 				#kind is declared
metadata:
  name: fluentd
  namespace: kube-logs				#namespace is declared
  labels:
    app: fluentd
---
apiVersion: rbac.authorization.k8s.io/v1
kind: ClusterRole					#need ClusterRole to grant access to 
							#cluster-scoped Nodes
metadata:
  name: fluentd
  labels:
    app: fluentd
rules:
- apiGroups:
  - ""
  resources:
  - pods
  - namespaces
  verbs:
  - get
  - list
  - watch
---
kind: ClusterRoleBinding			#It binds the ClusterRole to fluentd 								#service account
apiVersion: rbac.authorization.k8s.io/v1
metadata:
  name: fluentd
roleRef:
  kind: ClusterRole
  name: fluentd
  apiGroup: rbac.authorization.k8s.io
subjects:
- kind: ServiceAccount
  name: fluentd
  namespace: kube-logs
---
apiVersion: apps/v1
kind: DaemonSet					#Now DaemonSet spec begins
metadata:
  name: fluentd
  namespace: kube-logs
  labels:
    app: fluentd
spec:
  selector:
    matchLabels:
      app: fluentd
  template:
    metadata:
      labels:
        app: fluentd
    spec:
      serviceAccount: fluentd
      serviceAccountName: fluentd
      tolerations:
      - key: node-role.kubernetes.io/master
        effect: NoSchedule
      containers:
      - name: fluentd
        image: fluent/fluentd-kubernetes-daemonset:v1.4.2-debian-elasticsearch-1.1
        Env:					
          - name:  FLUENT_ELASTICSEARCH_HOST           # resolve to a list of IPs of
								           #3 Elasticsearch pods
            value: "elasticsearch.kube-logs.svc.cluster.local"
          - name:  FLUENT_ELASTICSEARCH_PORT
            value: "9200"
          - name: FLUENT_ELASTICSEARCH_SCHEME
            value: "http"
          - name: FLUENTD_SYSTEMD_CONF
            value: disable
        resources:
          limits:
            memory: 512Mi					#Memory limit 512MiB 
          requests:
            cpu: 100m						#Guarantee 0.1v CPU
            memory: 200Mi					#Guarantee  200MiB memory
        volumeMounts:
        - name: varlog
          mountPath: /var/log
        - name: varlibdockercontainers
          mountPath: /var/lib/docker/containers
          readOnly: true
      terminationGracePeriodSeconds: 30
      volumes:
      - name: varlog
        hostPath:
          path: /var/log
      - name: varlibdockercontainers
        hostPath:
          path: /var/lib/docker/containers
```

Save the file and roll out the DaemonSet and thus you can verify if it is running successfully :-

```
kubectl create -f fluentd.yaml
```
```
kubectl get ds --namespace=kube-logs
```

So finally we have 3 fluentd Pods running, which is corresponding to the number of nodes
in our Kubernetes cluster. Go to Kibana and verify that log data is being correctly collected and 
is being shipped to Elasticsearch.
In the Kibana Interface 
1. Click Discover (Left Navigation Menu)

![Discover](https://user-images.githubusercontent.com/62760235/108326697-902ead80-71f0-11eb-83a6-9cdff43658e0.png)

2. Choose to create Index Pattern
3. In the Index Pattern mention logstash-*. Using this we shall require all logs of our elasticsearch
   cluster.  Instead of logstash-* we can use any index pattern of our choice.
4. In Configure Settings select @timestamp in the dropdown and hit Create Index Pattern.
5. Now check Discover menu from the left to see logs.

![First Look](https://user-images.githubusercontent.com/62760235/108326979-e4d22880-71f0-11eb-92c3-fb22cd1a2c5d.png)

We have successfully configured and rolled out the EFK stack on your Kubernetes cluster. 
