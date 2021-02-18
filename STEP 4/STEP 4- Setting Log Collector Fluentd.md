Using Fluentd DaemonSet controller, we’ll roll out a Fluentd logging agent Pod on every node in our cluster. 

We shall move on to making fluentd.yaml file:-

```
nano fluentd.yaml
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
