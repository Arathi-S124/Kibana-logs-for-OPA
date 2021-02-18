In the namespace we are going to launch our Elasticsearch.  First we shall make file names elasticsearch_svc.yaml

```nano elasticsearch_svc.yaml```

Once the file is saved you shall proceed to apply it :-

```kubectl create -f elasticsearch_svc.yaml```

Confirm that your service is up and running:-

```kubectl get services --namespace=kube-logs```

Now that we have set the headless service and a stable .elasticsearch.kube-logs.svc.cluster.local domain for our Pods, we can go ahead and create the StatefulSet. We need StatefulSet because of the need for a stable identity for our pods and help them get a stable persistent storage.

Make a yaml file namely elasticsearch_statefulset.yaml

```nano elasticsearch_statefulset.yaml```

Now save the file and deploy the StatefulSet using:-

```kubectl create -f elasticsearch_statefulset.yaml```

Whenever needed we can monitor the Statefulset by:-

```kubectl rollout status sts/es-cluster --namespace=kube-logs```

We shall now forward the local port 9200 to port 9200 on one of the Elasticsearch nodes (es-cluster-0) because we need to check if elasticsearch cluster is working by making a request to the REST API. So use:-

```kubectl port-forward es-cluster-0 9200:9200 --namespace=kube-logs```

Then, in a separate terminal window, perform a curl request against the REST API:

```curl http://localhost:9200/_cluster/state?pretty```

The output shall indicate with information of all 3 nodes and we confirm that successfully we are set to move ahead.
