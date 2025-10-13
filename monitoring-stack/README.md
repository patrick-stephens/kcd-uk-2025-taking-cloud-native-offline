# Offline monitoring stack

An example set up to demonstrate taking our example monitoring stack offline with Zarf and Hauler.

Original set up from: <https://github.com/FluentDo/fluent-bit-examples/tree/main/monitoring-stack>.
This includes a few components:

* kube-prometheus-stack helm chart
  * Loki, Prometheus and Grafana images required
  * Custom Grafana dashboard
* fluent-bit helm chart
  * OSS Fluent Bit image
  * FluentDo Agent image
  * Forwarder (Daemonset) --> Aggregator (Deployment) model
