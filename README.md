# Monitoring your validator with Grafana and Prometheus

Prometheus is a monitoring platform that collects metrics from monitored targets by scraping metrics HTTP endpoints on these targets. Grafana is a dashboard used to visualize the collected data.

# Installation

Install prometheus and prometheus node exporter.

```json
sudo apt-get install -y prometheus prometheus-node-exporter
```

Install grafana.

```markdown
sudo apt-get install -y apt-transport-https
sudo apt-get install -y software-properties-common wget
sudo wget -q -O /usr/share/keyrings/grafana.key https://apt.grafana.com/gpg.key
echo "deb [signed-by=/usr/share/keyrings/grafana.key] https://apt.grafana.com stable main" | sudo tee -a /etc/apt/sources.list.d/grafana.list
sudo apt-get update && sudo apt-get install -y grafana
```

Enable services so they start automatically.

```markdown
sudo systemctl enable grafana-server.service prometheus.service prometheus-node-exporter.service
```

Create the prometheus.yml config file. Choose the tab for your eth client. Simply copy and paste.

```markdown
cat > $HOME/prometheus.yml << EOF
global:
  scrape_interval:     15s # By default, scrape targets every 15 seconds.

  # Attach these labels to any time series or alerts when communicating with
  # external systems (federation, remote storage, Alertmanager).
  external_labels:
    monitor: 'codelab-monitor'

# A scrape configuration containing exactly one endpoint to scrape:
# Here it's Prometheus itself.
scrape_configs:
   - job_name: 'node_exporter'
     static_configs:
       - targets: ['localhost:9100']
   - job_name: 'nodes'
     metrics_path: /metrics    
     static_configs:
       - targets: ['localhost:5054']
   - job_name: 'validators'
     metrics_path: /metrics
     static_configs:
       - targets: ['localhost:5064']
EOF
```

Move it to /etc/prometheus/prometheus.yml

```markdown
sudo mv $HOME/prometheus.yml /etc/prometheus/prometheus.yml
```

Update file permissions.

```markdown
sudo chmod 644 /etc/prometheus/prometheus.yml
```

Finally, restart the services.

```markdown
sudo systemctl restart grafana-server.service prometheus.service prometheus-node-exporter.service
```

Verify that the services are running properly:

```markdown
sudo systemctl status grafana-server.service prometheus.service prometheus-node-exporter.service
```


Setting up Grafana Dashboards

1. Open http://localhost:3000 or http://<your validator's ip address>:3000 in your web browser.
2. Login with admin / admin
3. Change password
4. Click the configuration gear icon, then Add data Source
5. Select Prometheus
6. Set Name to "Prometheus"
7. Set URL to http://localhost:9090
8. Click Save & Test
9. Download and save your consensus client's json file. More json dashboard options available on repository. [ Lighthouse | Teku | Nimbus | Prysm | Lodestar ]
10. Download and save your execution client's json file [ Geth | Besu | Nethermind | Erigon ]
11. Download and save a node-exporter dashboard for general system monitoring
12. Click Create + icon > Import
13. Add the consensus client dashboard via Upload JSON file
14. If needed, select Prometheus as Data Source.
15. Click the Import button.
16. Repeat steps 12-15 for the execution client dashboard.
17. Repeat steps 12-15 for the node-exporter dashboard.
