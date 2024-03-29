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

## Prerequisites
- [helm](https://helm.sh/docs/) - package manager for managing Kubernetes apps. Same as pip for Python.
- [kubectl](https://kubernetes.io/docs/tasks/tools/) - Kubernetes command line tool allowing you to run commands against clusters. Used to deploy apps, inspect and manage cluster resources.
- [awscli](https://aws.amazon.com/cli/) - AWC command line interface for managing services, controlling multiple services from command line and automating using scripts.
- [eksctl](https://eksctl.io/usage/creating-and-managing-clusters/) - CLI tool for creating and managing clusters on Amazon EKS.

The command:
```
eksctl create cluster
```
creates a cluster with auto-generated name, one nodegroup with two `m5.large` worker nodes, AWS EKS AMI, region configured in ~/.aws/config and dedicated VPC.

## Setting up Prometheus and Grafana

### Deploy microservice in EKS
```
eksctl create cluster
kubectl create namespace online-shop
kubectl apply -f config-microservices.yaml
```

### Deploying Prometheus Operator Stack
```
helm repo add prometheus-community https://prometheus-community.github.io/helm-charts
helm repo update
kubectl create namespace monitoring
helm install monitoring prometheus-community/kube-prometheus-stack -n monitoring
helm ls
```

### Check Prometheus Stack Pods
```
kubectl get all -n monitoring
```


### Access Prometheus UI
```
kubectl port-forward svc/monitoring-kube-prometheus-prometheus 9090:9090 -n monitoring &
```

### Access Grafana UI
```
kubectl port-forward svc/monitoring-grafana 8080:80 -n monitoring &
```

Defaults

Username: admin

Password: prom-operator

## Triggering CPU spike with Requests

### Deploying busybox pod to curl application
``` 
kubectl run curl-test --image=radial/busyboxplus:curl -i --tty --rm 
```
### Script curling app endpoint. Endpoint is external loadbalancer service endpoint

```
for i in $(seq 1 10000)
do
  curl ae4aee0715edc46b988c6ce-1459479566.us-east-1.elb.amazonaws.com > test.txt
done
```

## Accessing Alert manager UI
```
kubectl port-forward -n monitoring svc/monitoring-kube-prometheus-alertmanager 9093:9093 &

```

## Create CPU stress
```
kubectl delete pod cpu-test; kubectl run cpu-test --image=containerstack/cpustress -- --cpu 4 --timeout 60s --metrics-brief

```

## Deploying Redis Exporter
```
helm repo add prometheus-community https://prometheus-community.github.io/helm-charts
helm repo add stable https://charts.helm.sh/stable
helm repo update

helm install redis-exporter prometheus-community/prometheus-redis-exporter -f redis-values.yaml
```



## Important links:
1. [Prometheus Client Libraries](https://prometheus.io/docs/instrumenting/clientlibs/)
2. [Collection of alerting rules](https://samber.github.io/awesome-prometheus-alerts/)
3. [Prometheus Helm Chart](https://github.com/prometheus-community/helm-charts)
4. [kube-prometheus-stack](https://github.com/prometheus-community/helm-charts/tree/main/charts/kube-prometheus-stack) - Installs the kube-prometheus stack, a collection of Kubernetes manifests, Grafana dashboards, and Prometheus rules combined with documentation and scripts to provide easy to operate end-to-end Kubernetes cluster monitoring with Prometheus using the Prometheus Operator.

Credits: [Tech With Nana DevOps Bootcamp](https://www.techworld-with-nana.com/devops-bootcamp)
