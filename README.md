# EFK-setup
A simple EFK(Elasticsearch, Fluentd, Kibana) stack for centralized logging

To check the logs of microservices in a kubernetes environment can be little difficult. You have to read the logs from each individual
pods/containers where your app is deployed. To overcome this issue we have EFK stack for centralized logging.

## How EFK stack works
Flunetd(log collector) runs as a daemonset within each node of the cluster and pushes the log to the Elasticsearch. After this, we can visualize these logs from Kibana dashboard. So basically we now have a centralized logging system for all our apps in the kubernetes cluster.

## Deploy elasticsearch and kibana
Create elasticsearch and kibana deployment by using their corresponding .yaml files.<br>
e.g. : ```kubectl apply -f <yaml file>```


## Configure fluentd
We can have custom configurations for fluentd to parse, filter and modify the logs before pushing into elasticsearch.
Here are some the configurations we can have: <br>
  1. exclude kube-system logs: <br>
       ```<match kubernetes.var.log.containers.**kube-system**.log><br>@type null <br></match>```
