### Required Tools
---
- Helm
- Kubectl
- Prometheus
- Loki

### 1. Apply Alloy Operator
---
```bash
$ kubectl apply -f https://github.com/grafana/alloy-operator/releases/latest/download/collectors.grafana.com_alloy.yaml
```

### 2. Add Grafana Helm Repo
---
```bash
$ helm repo add grafana https://grafana.github.io/helm-charts
$ helm repo update
```

### 3. Create a k8s-monitoring Value File
---
!!! warning

    Must Enable **--web.enable-remote-write-receiver** settings in prometheus because prometheus is Pull mode so to Push the Metrics for K8s Grafana-Alloy must enable this settings, if you forget to enable the this settings metrics will not push from Grafana-Alloy and in `grafana-k8s-monitoring-alloy-metrics` pod it will thorw Error.

```yaml
cluster:
  name: <Enter Cluster Name>

destinations:
- name: localPrometheus
  type: prometheus
  url: http://<prometheus-host>:9090/api/v1/write

- name: localLoki
  type: loki
  url: http://<loki-host>:3100/loki/api/v1/push

# enable cluster metrics
clusterMetrics:
  enabled: true

# enable pod logs + node logs if you want them
podLogs:
  enabled: true
nodeLogs:
  enabled: false # INFO : I Disable this dude to pods error wand to enable this again and check for error, if you get pod log error set this to false.

# enable cluster events (optional)
clusterEvents:
  enabled: true

# turn on collectors
alloy-metrics:
  enabled: true
alloy-logs:
  enabled: true
alloy-singleton:
  enabled: true
```

For more Destinations Here is Doc.

Main Doc : [**Link**](https://github.com/grafana/k8s-monitoring-helm/blob/main/charts/k8s-monitoring/docs/destinations/README.md)

This section defines the destinations for your telemetry data. You can configure multiple destinations for logs,
metrics, and traces. Here are the supported destination types:

!!! warning

    In this Grafana k8s-Monitoring Offical Doc They not Defain the **destinations.url** in yaml properly, you must include after the destinations host and port you need to set path.

    - `http://<prometheus-host>:<prometheus-port>` : This Will Not work, it will throw error in grafana-k8s-monitoring pod.
    - `http://<prometheus-host>:<prometheus-port>/<path>` : Wand to set the correct path to Push Path to send the data to destinations.

    examples:

    - prometheus : `http://<prometheus-host>:9090/api/v1/write`
    - loki : `http://<loki-host>:3100/loki/api/v1/push`

| Type         | Protocol         | Telemetry Data        | Docs                                      |
|--------------|------------------|-----------------------|-------------------------------------------|
| `prometheus` | Remote Write     | Metrics               | [Docs](https://github.com/grafana/k8s-monitoring-helm/blob/main/charts/k8s-monitoring/docs/destinations/prometheus.md) |
| `loki`       | Loki             | Logs                  | [Docs](https://github.com/grafana/k8s-monitoring-helm/blob/main/charts/k8s-monitoring/docs/destinations/loki.md)       |
| `otlp`       | OTLP or OTLPHTTP | Metrics, Logs, Traces | [Docs](https://github.com/grafana/k8s-monitoring-helm/blob/main/charts/k8s-monitoring/docs/destinations/otlp.md)       |
| `pyroscope`  | Pyroscope        | Profiles              | [Docs](https://github.com/grafana/k8s-monitoring-helm/blob/main/charts/k8s-monitoring/docs/destinations/pyroscope.md)  |

### 4. Deploy The grafana-k8s-monitoring
---
```bash
$ helm install grafana-k8s-monitoring grafana/k8s-monitoring -n grafana -f "./values.yaml"
```