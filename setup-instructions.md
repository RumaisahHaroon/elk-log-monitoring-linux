# 🛠️ ELK Stack with Filebeat – Setup Instructions

This guide explains how to set up a centralized log monitoring system on a Linux (Ubuntu) system using the **ELK Stack (Elasticsearch, Logstash, Kibana)** and **Filebeat**, with all components containerized using Docker.

---

## 📦 Prerequisites

- Ubuntu (tested on Ubuntu 20.04 or later)
- Docker installed
- Docker Compose installed
- Internet access

---

## 🔧 1. Install Docker and Docker Compose

Update and upgrade the system:

```bash
sudo apt update
sudo apt upgrade -y
```

Install Docker:

```bash
sudo apt install docker.io -y
sudo systemctl enable docker
sudo systemctl start docker
```

Install Docker Compose:

```bash
sudo apt install docker-compose -y
```

Verify installation:

```bash
docker --version
docker-compose --version
```

---

## 📁 2. Create Project Directory

```bash
mkdir elk
cd elk
```

---

## 🧱 3. Create `docker-compose.yml`

Create the file:

```bash
nano docker-compose.yml
```

Paste the following:

```yaml
version: '3.7'
services:
  elasticsearch:
    image: docker.elastic.co/elasticsearch/elasticsearch:7.17.10
    container_name: elasticsearch
    environment:
      - discovery.type=single-node
      - xpack.security.enabled=false
    ports:
      - 9200:9200
    mem_limit: 1g

  logstash:
    image: docker.elastic.co/logstash/logstash:7.17.10
    container_name: logstash
    volumes:
      - ./logstash.conf:/usr/share/logstash/pipeline/logstash.conf
    ports:
      - 5044:5044
```

Save and exit.

---

## 📄 4. Create `logstash.conf`

```bash
nano logstash.conf
```

Paste the following:

```conf
input {
  beats {
    port => 5044
  }
}

filter {
  grok {
    match => { "message" => "%{SYSLOGTIMESTAMP:timestamp} %{SYSLOGHOST:host} %{GREEDYDATA:message}" }
  }
}

output {
  elasticsearch {
    hosts => ["http://elasticsearch:9200"]
    index => "filebeat-%{+YYYY.MM.dd}"
  }
}
```

Save and exit.

---

## ▶️ 5. Start ELK Stack

```bash
sudo docker-compose up -d
```

Check running containers:

```bash
sudo docker ps
```

Wait 1–2 minutes, then go to:

```
http://localhost:5601
```

You should see the Kibana UI.

---

## 📥 6. Install Filebeat

Download and install:

```bash
curl -L -O https://artifacts.elastic.co/downloads/beats/filebeat/filebeat-8.14.0-amd64.deb
sudo dpkg -i filebeat-8.14.0-amd64.deb
sudo apt-get install -f
filebeat version
```

---

## ⚙️ 7. Configure Filebeat

Edit config file:

```bash
sudo nano /etc/filebeat/filebeat.yml
```

### Update output section:

```yaml
# Disable Elasticsearch output
#output.elasticsearch:
#  hosts: ["localhost:9200"]

# Enable Logstash output
output.logstash:
  hosts: ["localhost:5044"]
```

### Enable input logs:

```yaml
filebeat.inputs:
- type: log
  enabled: true
  paths:
    - /var/log/syslog
    - /var/log/auth.log
```

Save and exit.

---

## 🔁 8. Restart and Enable Services

Restart Logstash:

```bash
docker restart logstash
```

Start Filebeat:

```bash
sudo systemctl start filebeat
sudo systemctl enable filebeat
```

Check Filebeat status:

```bash
sudo systemctl status filebeat
```

---

## 📊 9. Visualize Logs in Kibana

- Visit: `http://localhost:5601`
- Go to **Discover**
- Set index pattern to: `filebeat-*`
- Click **Create Index Pattern**

You should now see logs from your system (e.g., syslog, auth.log).

---

## ✅ Done!

Your centralized log monitoring setup is complete. Logs are now being shipped from your Linux system to Logstash, stored in Elasticsearch, and visualized in Kibana.

---

## 🔮 Next Steps (Optional)

- Add **Packetbeat** for real-time network traffic monitoring
- Set up alerting in Kibana
- Secure Elasticsearch and Kibana with authentication
