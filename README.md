# 🚀 Hadoop Cluster con Docker Compose (incluye Airflow y NiFi)

Este repositorio levanta un **mini-ecosistema Big Data en local** usando **Docker Compose**, incluyendo:

- **HDFS**: NameNode + múltiples DataNodes  
- **YARN**: ResourceManager + NodeManagers  
- **History Server**: historial de jobs MapReduce completados  
- **Apache Airflow**: orquestación de pipelines (con **Postgres** como backend)  
- **Apache NiFi**: ingesta y gestión de flujos de datos  

Todo corre en contenedores separados y conectados dentro de una red Docker común (por ejemplo `cluster_net`).

---

## 📦 Requisitos

- Docker Desktop (o Docker Engine) instalado
- Docker Compose disponible (`docker compose ...`)

---

## 🗂️ Estructura del proyecto

Ejemplo típico:

```text
project/
├─ docker-compose.yml
├─ env
├─ dags/
└─ volumes/           (creados automáticamente por Docker)
