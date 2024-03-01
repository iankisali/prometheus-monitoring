# Monitoring and Alerting using Prometheus and Grafana
Monitoring workloads using Prometheus and Grafana.
**Prometheus** is open source system designed for monitoring and alerting functionality for cloud environments, including kubernetes.
**Grafana** is open source platform for analytics and interactive visualization web app.
Prometheus collects metrics, allowing time series data aggregation and provides queryng language (PromQL) while Grafana tranforms the metrics obtained into meaningful visualization.

Prometheus ecosystem consists of:
- **Prometheus server**: Scrapes and stores time series data. Components of server include data retrieval worker(retreival and pulling metrics), time series database(storage) and HTTP server(accepts PromQL queries).
- **Exporter**: fetch metrics from target and converts to correct format. 
- **Alermanager**: handles alerts.
- **Push gateway**: supports short-lived jobs.

## Prometheus Architecture
![Prometheus](https://prometheus.io/assets/architecture.png)
Courtesy of [Prometheus Docs](https://prometheus.io/docs/introduction/overview/)

Targets can be server, application, database etc and metrics can be cpu status, units, IOPS, memory usage etc.
Prometheus has its own /metrics endpoint
**Prometheus operator** manages all prometheus components while **prometheus federation** scrapes data from other prometheus server.

## Alerting Overview
Alerting is separated into:
1. Alerting rules in Prometheus server sent to AlertManager
2. Alertmanager manages all alerts and send notifications.

Main steps of setting up Grafana include:
- Setup and configure AlertManager
- Configure prometheus to talk to alertmanager
- Create alerting rules in prometheus




## Important links:
1. [Prometheus Client Libraries](https://prometheus.io/docs/instrumenting/clientlibs/)
2. [Collection of alerting rules](https://samber.github.io/awesome-prometheus-alerts/)
3. [Prometheus Helm Chart](https://github.com/prometheus-community/helm-charts)
4. [kube-prometheus-stack](https://github.com/prometheus-community/helm-charts/tree/main/charts/kube-prometheus-stack) - Installs the kube-prometheus stack, a collection of Kubernetes manifests, Grafana dashboards, and Prometheus rules combined with documentation and scripts to provide easy to operate end-to-end Kubernetes cluster monitoring with Prometheus using the Prometheus Operator.
