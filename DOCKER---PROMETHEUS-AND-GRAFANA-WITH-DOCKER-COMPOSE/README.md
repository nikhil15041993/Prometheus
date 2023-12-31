# DOCKER PROMETHEUS-AND-GRAFANA-WITH-DOCKER-COMPOSE

To run Prometheus and Grafana with docker compose, we need to create a docker compose file defining the individual services (Prometheus and Grafana), the images to be used, ports running on and any other thing necessary.

```
version: '3.3'

networks:
  monitoring:
    driver: bridge

volumes:
  prometheus_data: {}


services:
  prometheus:
    command:  
    - --config.file=/etc/prometheus/prometheus.yml
    - --storage.tsdb.path=/data/prometheus
    
    container_name: prometheus
    #environment:
    # LISTEN_ADDRESS: 127.0.0.1
    
    
    ports:
      - "9090:9090"        

    image: prom/prometheus:latest
    user: root
    restart: unless-stopped
    networks: 
      - monitoring
    volumes:
      - /etc/prometheus/prometheus.yml:/etc/prometheus/prometheus.yml
      - prometheus_data:/data/prometheus        



  nodeexporter:
    image: prom/node-exporter:latest
    container_name: nodeexporter
    restart: unless-stopped
    ports:
      - "9100:9100"

    networks:
      - monitoring    



  grafana:
    image: grafana/grafana:8.3.3
    container_name: grafana 
    environment:
      - GF_SECURITY_ADMIN_USER=${ADMIN_USER:-admin}
      - GF_SECURITY_ADMIN_PASSWORD=${ADMIN_PASSWORD:-admin}
      - GF_USERS_ALLOW_SIGN_UP=false

    restart: unless-stopped
    ports:
      - "3000:3000" 
      
 ```
 
 
 ######################################################################################
                 prometheus.yml
########################################################################################

```
# my global config
global:
  scrape_interval: 15s # Set the scrape interval to every 15 seconds. Default is every 1 minute.
  evaluation_interval: 15s # Evaluate rules every 15 seconds. The default is every 1 minute.
  # scrape_timeout is set to the global default (10s).

# Alertmanager configuration
alerting:
  alertmanagers:
    - static_configs:
        - targets:
           - alertmanager:9093

# Load rules once and periodically evaluate them according to the global 'evaluation_interval'.
rule_files:
  - "prometheus_rules.yml"       
  # - "first_rules.yml"
  # - "second_rules.yml"

# A scrape configuration containing exactly one endpoint to scrape:
# Here it's Prometheus itself.
scrape_configs:
  # The job name is added as a label `job=<job_name>` to any timeseries scraped from this config.
  - job_name: "prometheus"

    # metrics_path defaults to '/metrics'
    # scheme defaults to 'http'.

    static_configs:
      - targets: ["localhost:9090"]

  - job_name: "node-exporter"

    # metrics_path defaults to '/metrics'
    # scheme defaults to 'http'.

    static_configs:
      - targets: ["localhost:9100"]  
   
  ``` 
 
 
     
