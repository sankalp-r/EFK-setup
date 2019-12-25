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
We can have custom configurations for fluentd to parse, filter and modify the logs before pushing into elasticsearch. To have your own fluentd configuration, we need to create a ConfigMap in our cluster(see the configmap.yaml file). 
Here are some of the examples of sample configurations: <br>
  1. exclude kube-system logs: <br>
       ```
       <match kubernetes.var.log.containers.**kube-system**.log>
         @type null
       </match>
      ```
  2. exclude fluentd logs: <br>
       ```
       <match kubernetes.var.log.containers.**fluentd**.log>
         @type null
       </match>
      ```
  3. parse the log:<br>
     Suppose we have a log-message : ```2019/12/23 00:12:36 [DEBUG] [App]: debug-message``` <br>
     We have to transform this message into json-form: <br>
        ```
        { 
           "timestamp": 2019/12/23 00:12:36,
           "level": "DEBUG",
           "component": "App",
           "message": "debug-message"
        }
        ```
      Here is the configuration you need to tranform the log-message in the above mentioned format: <br>
         ```
         <filter kubernetes.**>
           @type parser
           key_name log
           <parse>
               @type regexp
               expression /^(?<timestamp>.*?)\s\[(?<level>.*?)\] \[(?<component>.*?)\]: (?<message>.*)$/
           </parse>
         </filter>
         ```
