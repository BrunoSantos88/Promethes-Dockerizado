O que é Prometeus?
Prometheus é um kit de ferramentas de alerta e monitoramento de sistemas de código aberto originalmente desenvolvido no SoundCloud . Desde a sua criação em 2012, muitas empresas e organizações adotaram o Prometheus, e o projeto tem uma comunidade de desenvolvedores e usuários muito ativa . Agora é um projeto independente de código aberto e mantido de forma independente por qualquer empresa. Para enfatizar isso e esclarecer a estrutura de governança do projeto, o Prometheus juntou-se à Cloud Native Computing Foundation em 2016 como o segundo projeto hospedado, depois do Kubernetes .

# Clona repository

``` Acessar pasta Desafio_o2b
git clone https://github.com/BrunoSantos88/Desafio_o2b-Observability.git
cd Desafio_o2b-Observability
``` 

# Install
``` Dependencias
 pip install Flask prometheus_client
```
docker.compose.yml
``` yml
version: '3'

services:

  aplication:
    build:
      context: .
      dockerfile: dockerfile
    ports:
      - 3001:3001
    environment:
      - PYTHONUNBUFFERED=1
    depends_on:
      - prometheus

  prometheus:
    image: prom/prometheus
    ports:
      - 9090:9090
    volumes:
      - ./prometheus/prometheus.yml:/etc/prometheus/prometheus.yml
      - ./prometheus/rules.yml:/etc/prometheus/rules.yml
    command:
      - '--config.file=/etc/prometheus/prometheus.yml'
    network_mode: "host"

  node-exporter:
    image: prom/node-exporter
    ports:
      - 9100:9100
    network_mode: "host"
 
  alertmanager:
    image: prom/alertmanager
    container_name: alertmanager
    ports:
     - '9093:9093'
    volumes:
      - ./alertmanager.yml:/etc/alertmanager/alertmanager.yml
    network_mode: "host"

  cadvisor:
    image: gcr.io/cadvisor/cadvisor:latest
    container_name: cadvisor
    ports:
    - 8080:8080
    volumes:
    - /:/rootfs:ro
    - /var/run:/var/run:rw
    - /sys:/sys:ro
    - /var/lib/docker/:/var/lib/docker:ro
    network_mode: "host"

  grafana:
    image: grafana/grafana
    container_name: grafana
    ports:
      - 3000:3000
    network_mode: "host"

volumes:
  grafana-data:

````
# killerKoda
- Linux (baseado no Ubuntu)
- Docker
- Prometheus
- Grafana
- Alertmanager
- python
  
# Link Guias

- Setup observability
- Link: https://dev.to/danielfavour/container-monitoring-ensuring-application-performance-and-health-kcj

- Alert Manager
- Link: https://samber.github.io/awesome-prometheus-alerts/rules.html#docker-containers

# Promethues 
- Link https://prometheus.io/docs/introduction/overview/

# prometheus.yml
  ```
global:
  scrape_interval: 5s
  evaluation_interval: 10s

  
rule_files:
  - rules.yml
alerting:
  alertmanagers:
  - static_configs:
    - targets:
       - localhost:9093
       
scrape_configs:
  - job_name: 'prometheus'
    static_configs:
      - targets: ['localhost:9090']
  - job_name: 'node-exporter'
    static_configs:
      - targets: ['localhost:9100']
  - job_name: python_server
    scrape_interval: 5s
    metrics_path: /metrics
    static_configs:
      - targets: ["localhost:3001"]
  - job_name: cadvisor
    scrape_interval: 5s
    static_configs:
    - targets: ["localhost:8080"]
  - job_name: 'node'
    static_configs:
      - targets: ['localhost:9100']

```

# Rules
- Link https://prometheus.io/docs/prometheus/latest/configuration/alerting_rules/

  # rules.yml
````
  groups:
 - name: Count greater than 5
   rules:global:
  resolve_timeout: 5m
route:
  receiver: webhook_receiver
receivers:
    - name: webhook_receiver
      webhook_configs:
        - url: 'https://webhook.site/49627f3d-1930-47af-8c80-2a63f9378bcd'
          send_resolved: false
   - alert: CountGreaterThan5
     expr: ping_request_count > 5
     for: 10s
  ````

# AlertMaanager

- Link: https://prometheus.io/docs/alerting/latest/alertmanager/

  # alertmanager.yml

````
global:
  resolve_timeout: 5m
route:
  receiver: webhook_receiver
receivers:
    - name: webhook_receiver
      webhook_configs:
        - url: 'https://webhook.site/49627f3d-1930-47af-8c80-2a63f9378bcd'
          send_resolved: false
  ````
  

# Webhook
- Link https://webhook.site/#!/49627f3d-1930-47af-8c80-2a63f9378bcd/1140d711-fad3-4189-8e9e-f1a6b706d7a3/1
