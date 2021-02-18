## VISUALIZE OPA LOGS ON KIBANA AND MANAGE ALERTS

We intend to deploy our OPA policies on Kubernetes cluster and the respective logs to be understood with visualization. We also shall see how to make alerts and actions. We are making use of EFK stack.

### What is EFK ?

#### → E (elasticsearch)
Elasticsearch is search engine which primarily allows you to store, search, and analyze large sets of data fastly and in almost real-time so as to provide answers of searches in milliseconds. It makes use of index patterns as the most efficient way of fetching responses instead of searching large lengths of text.

#### → F (Fluentd)
​Fluentd is a known open-source data collector which is used for a unified logging layer. It helps you to unify data collection, maintaining logs and thus to understand the data in a better way.

#### → K (Kibana)
Kibana is a data visualization and management tool which is used along with Elasticsearch so as to give real-time charts like:- histograms, line graphs, pie charts, and maps. Kibana has integrated tool like APM that allows us to create alerts and unify it with actions on platforms like mails, slack etc. 

### Why EFK Stack?
When EFK is integrated it helps in aggregating logs from hosts and applications, it doesn’t matter how many containers are deployed the data is collected from the deployed platform and even it gives logs of deleted pods. elasticsearch acts as an object store where logs are stored and the responsibility of providing it, data and logs, is of Fluentd. Finally we have web user interface for the data on Kibana. 

### Requirements in System:-
A Kubernetes 1.10+ cluster with role-based access control (RBAC) enabled
Kubectl command line tool

Following is all the steps that is to be followed to achieve successful EFK setup to receiving alerts of OPA policies on desired platform.
