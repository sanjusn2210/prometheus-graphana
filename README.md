
# Prometheus and Grafana
This is a collection of configuration files for docker compose stacks for Prometheus, node-exporter, CAdvisor, AlertManager and Grafana.
It becomes handy when you want to run a stack of docker containers and you don't want to run many command.


# Prometheus and Grafana Installation

### 1. Install Docker
```bash
curl -s https://raw.githubusercontent.com/jaintpharsha/install/main/docker | sudo bash
```
### 2. Clone Prometheus-grafana setup repo 
```bash
git clone https://github.com/harshaprakash100/prometheus_grafana.git && cd ./prometheus_grafana
```

### 3. Run promethes grafana CAdvisor alertmanager node-exporter
```bash    
docker compose up -d 
```
### 4. Add new scrap_configs in prometheus/prometheus.yml
   Restart the prometheus container to apply new scrap configurations

## Demo

**To access Prometheus Dashboard:** <public_ip>:9090
    
**To check all the prometheus scraps targets:** <public_ip>:9090/targets 
    
**To access Grafana Dashboard:** <public_ip>:3000 <br/>
  **Note:** username and password configuration file: grafana/config.monitoring


# kube-state-metrics Installation

### 1. Install Kube-State-Metrics objects in K8S cluster
	
Installing using Manifest
```bash
git clone https://github.com/kubernetes/kube-state-metrics && \
kubectl apply -f kube-state-metrics/examples/standard/
```	
Installing using Helm Charts
```bash
helm repo add prometheus-community https://prometheus-community.github.io/helm-chartshelm repo updatehelm install kube-state-metrics prometheus-community/kube-state-metrics -n kube-system
```

Expose state-metrics with NodePort 
```bash
kubectl expose service -n kube-system kube-state-metrics --type=NodePort --target-port=8080 --name=kube-state-metrics-np
```

### 2. Add scrap configuration in prometheus/prometheus.yaml
```yml
- job_name: 'kube-state-metrics'
    scrape_interval: 5s
    honor_timestamps: true
    metrics_path: /metrics
    static_configs:
      - targets: ['<k8s_node_public_ip>:<NodePort>']
    metric_relabel_configs:
      - target_label: cluster
        replacement: ITDK8sCluster
```
Restart Prometheus container to apply scrap configuration
```bash
sudo docker restart <prometheus_container>
```

### 3. In grafana Add data source with promethes server url and create dashboard with ID: 13332
```bash
13332
```

### 4. Please wait for some time to allow the data to be reflected in the Grafana dashboard.
# prometheus-graphana
# prometheus-graphana
