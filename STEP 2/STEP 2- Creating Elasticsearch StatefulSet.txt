In the namespace we are going to launch our Elasticsearch.  First we shall make file names elasticsearch_svc.yaml

```
kind: Service 					#defining kind of object
apiVersion: v1
metadata:
  name: elasticsearch				#stating name 
  namespace: kube-logs		 	#referring to the namespace which is in use
  labels:
    app: elasticsearch				
spec:
  selector:
    app: elasticsearch
  clusterIP: None
  Ports:					
    - port: 9200				#define port to interact with REST API 
      name: rest
    - port: 9300				##define port for inter node communication
      name: inter-node
```

Once the file is saved you shall proceed to apply it :-

```
kubectl create -f elasticsearch_svc.yaml
```
Confirm that your service is up and running:-

```
kubectl get services --namespace=kube-logs
```
Now that we have set the headless service and a stable .elasticsearch.kube-logs.svc.cluster.local domain for our Pods, we can go ahead and create the StatefulSet. We need StatefulSet because of the need for a stable identity for our pods and help them get a stable persistent storage. Make a yaml file namely elasticsearch_statefulset.yaml

```
apiVersion: apps/v1
kind: StatefulSet					#defines object kind
metadata:
  name: es-cluster
  namespace: kube-logs	
spec:
  serviceName: elasticsearch
  replicas: 3						#defines no. of pods
  selector:
    matchLabels:
      app: elasticsearch
  template:
    metadata:
      labels:
        app: elasticsearch
    spec:
      containers:
      - name: elasticsearch			#naming the container
        image: docker.elastic.co/elasticsearch/elasticsearch:7.2.0   #Tag 7.2.0 can 												be changed 	      resources:
            limits:
              cpu: 1000m				#container needs at least 0.1 vCPU to it
            requests:
              cpu: 100m
        ports:
        - containerPort: 9200
          name: rest
          protocol: TCP
        - containerPort: 9300
          name: inter-node
          protocol: TCP
        volumeMounts:
        - name: data				#mount the PersistentVolume named data to 	
					             the container at the path specified ahead
          mountPath: /usr/share/elasticsearch/data
        env:
          - name: cluster.name
            value: k8s-logs
          - name: node.name
            valueFrom:
              fieldRef:
                fieldPath: metadata.name
          - name: discovery.seed_hosts
            value: "es-cluster-0.elasticsearch,es-cluster-1.elasticsearch,es-cluster-2.elasticsearch"
          - name: cluster.initial_master_nodes
            value: "es-cluster-0,es-cluster-1,es-cluster-2"
          - name: ES_JAVA_OPTS
            value: "-Xms512m -Xmx512m"
      initContainers:
      - name: fix-permissions
        image: busybox
        command: ["sh", "-c", "chown -R 1000:1000 /usr/share/elasticsearch/data"]
        securityContext:
          privileged: true
        volumeMounts:
        - name: data
          mountPath: /usr/share/elasticsearch/data
      - name: increase-vm-max-map
        image: busybox
        command: ["sysctl", "-w", "vm.max_map_count=262144"]
        securityContext:
          privileged: true
      - name: increase-fd-ulimit
        image: busybox
        command: ["sh", "-c", "ulimit -n 65536"]
        securityContext:
          privileged: true
  volumeClaimTemplates:
  - metadata:
      name: data
      labels:
        app: elasticsearch
    spec:
      accessModes: [ "ReadWriteOnce" ]
      storageClassName: do-block-storage
      resources:
        requests:
          storage: 100Gi
```

Now save the file and deploy the StatefulSet using:-

```
kubectl create -f elasticsearch_statefulset.yaml
```

Whenever needed we can monitor the Statefulset by:-

```
kubectl rollout status sts/es-cluster --namespace=kube-logs
```

We shall now forward the local port 9200 to port 9200 on one of the Elasticsearch nodes (es-cluster-0) because we need to check if elasticsearch cluster is working by making a request to the REST API. So use:-

```
kubectl port-forward es-cluster-0 9200:9200 --namespace=kube-logs
```

Then, in a separate terminal window, perform a curl request against the REST API:

```
curl http://localhost:9200/_cluster/state?pretty
```

The output shall indicate with information of all 3 nodes and we confirm that successfully we are set to move ahead.
