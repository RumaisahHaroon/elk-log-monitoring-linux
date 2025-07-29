# 🛡️ Centralized Log Monitoring on Linux using ELK Stack & Filebeat

This project demonstrates how to set up centralized log monitoring using the ELK Stack (Elasticsearch, Logstash, Kibana) along with Filebeat on a Linux system. All services are containerized using Docker.

## 🔧 Tools Used
- Docker & Docker Compose
- Elasticsearch
- Logstash
- Kibana
- Filebeat
- Ubuntu Linux

## 📦 Project Structure
- `docker-compose.yml` – to spin up ELK stack services
- `logstash.conf` – defines the Logstash pipeline
- `filebeat.yml` – configures Filebeat to collect `/var/log/syslog` and `/var/log/auth.log`
- project report

## ⚙️ Setup Instructions
See [`setup.md`](setup.md) for detailed step-by-step setup.

## 📊 Kibana Dashboard Preview
![IMG-20250727-WA0005](https://github.com/user-attachments/assets/5182a4d7-c880-45c0-a3b7-579cd8b1de53)
![IMG-20250727-WA0006](https://github.com/user-attachments/assets/8292db22-befc-40f7-a608-076544e8b47e)


## 🚀 Future Improvements
- Integrate Packetbeat for network monitoring
- Set up alerting using Watcher or third-party tools

---

> Feel free to fork, contribute, or ask questions. This project helped me understand the power of centralized logging and Dockerized deployments.

