# Deploy metrics Kafka

## Deploy Metrics Platform

Prometheus and Grafana Operator help us to deploy our local Prometheus Server and Grafana instance where
we could manage the metrics from our Apache Kafka Cluster.

To deploy a local Prometheus Server in `redhat-lab` namespace.

```
oc create secret generic additional-scrape-configs --from-file=prometheus-additional.yaml=metrics/prometheus/prometheus-additional.yml
oc create -f metrics/prometheus/strimzi-service-monitor.yml
oc create -f metrics/prometheus/strimzi-pod-monitor.yml
oc create -f metrics/prometheus/prometheus-rules.yml
oc create -f metrics/prometheus/prometheus.yml
```

A Prometheus instance will be available with a service and a route:

```
$ oc get svc
NAME                  TYPE        CLUSTER-IP       EXTERNAL-IP   PORT(S)    AGE
prometheus            ClusterIP   172.30.224.132   <none>        9090/TCP   65s
prometheus-operated   ClusterIP   None             <none>        9090/TCP   65s
```

Deploy Grafana:

```
oc apply -f metrics/grafana/grafana.yml
```

Grafana will deploy a Datasource connected to the Prometheus server available by Prometheus
in the endpoint `http://prometheus-operated:9090`. The Grafana Server will use that Datasource
to get the metrics.

Grafana has a set of dashboards to review the metrics from Apache Zookeeper and Apache Kafka Cluster. We could
deploy it as:

```
oc apply -f metrics/grafana/dashboards/grafana-dashboard-strimzi-kafka.yml
oc apply -f metrics/grafana/dashboards/grafana-dashboard-strimzi-zookeeper.yml
oc apply -f metrics/grafana/dashboards/grafana-dashboard-strimzi-kafka-exporter.yml
oc apply -f metrics/grafana/dashboards/grafana-dashboard-strimzi-operators.yml
oc apply -f metrics/grafana/dashboards/grafana-dashboard-strimzi-kafka-connect.yml
oc apply -f metrics/grafana/dashboards/grafana-dashboard-strimzi-kafka-mirror-maker-2.yml
```

To get the route to access Grafana:

```
oc get route grafana-route -o jsonpath='{.spec.host}'
```

Use the original credentials **root/secret** as user/password. These credentials are defined in the
[Grafana CR](blob/main/metrics/grafana/grafana.yml) file.


