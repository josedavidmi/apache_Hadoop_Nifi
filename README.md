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
```

- `docker-compose.yml`: define servicios, puertos, redes y volúmenes
- `env`: variables de entorno compartidas por servicios Hadoop
- `dags/`: DAGs de Airflow
- Volúmenes Docker: persisten datos (NameNode, DataNodes, Postgres, etc.)

---

## 🧩 Servicios principales

### 1) NameNode (HDFS)
El “cerebro” de HDFS: gestiona metadatos y ubicación de bloques.

Ejemplo (fragmento de compose):

```yaml
namenode:
  image: apache/hadoop:3
  command: bash -c "if [ ! -d /tmp/hadoop-root/dfs/name/current ]; then echo Y | hdfs namenode -format; fi && hdfs namenode"
  ports:
    - "9870:9870"
```

✅ UI NameNode: `http://localhost:9870`

---

### 2) DataNodes (HDFS)
Almacenan los bloques de datos en HDFS. Varios DataNodes = almacenamiento distribuido.

Ejemplo:

```yaml
datanode1:
  image: apache/hadoop:3
  command: ["hdfs", "datanode"]
  ports:
    - "9864:9864"
```

✅ UIs DataNode (según tu compose):  
- `http://localhost:9864`  
- `http://localhost:9865` *(si hay un segundo DataNode con ese mapeo)*

---

### 3) YARN (ResourceManager + NodeManagers)
Gestiona recursos del clúster y planificación de jobs.

Ejemplo ResourceManager:

```yaml
resourcemanager:
  image: apache/hadoop:3
  command: ["yarn", "resourcemanager"]
  ports:
    - "8088:8088"
```

✅ UI ResourceManager: `http://localhost:8088`

---

### 4) History Server (MapReduce)
Mantiene el historial de jobs MapReduce.

Ejemplo:

```yaml
historyserver:
  image: infravibe/hadoop:3.3.6
  command: bash -c "mapred --daemon start historyserver && tail -f /dev/null"
  ports:
    - "19888:19888"
```

✅ UI JobHistory: `http://localhost:19888`

---

### 5) Airflow + Postgres
Orquestación de workflows/pipelines con Airflow usando Postgres como base de metadatos.

Ejemplo:

```yaml
airflow:
  image: apache/airflow:2.1.0
  ports:
    - "8080:8080"
```

✅ UI Airflow: `http://localhost:8080`  
> Usuario/contraseña por defecto (si tu compose lo configura así): `admin / admin`

---

### 6) Apache NiFi
Ingesta, transformación y enrutamiento de datos mediante flujos.

Ejemplo:

```yaml
nifi:
  image: apache/nifi:latest
  ports:
    - "8888:8888"
```

✅ UI NiFi: `http://localhost:8888`

---

## ▶️ Cómo levantar el entorno

1) Clona el repositorio (si aplica):

```bash
git clone https://github.com/samuelsantosdev/docker-apache_airflow.git
cd docker-apache_airflow
```

2) Levanta todo:

```bash
docker-compose up -d
```

> Si usas Docker Compose v2:
```bash
docker compose up -d
```

3) Verifica que está todo arriba:

```bash
docker ps
```

---

## 🔗 URLs útiles

- NameNode (HDFS): `http://localhost:9870`
- YARN ResourceManager: `http://localhost:8088`
- History Server: `http://localhost:19888`
- Airflow: `http://localhost:8080`
- NiFi: `http://localhost:8888`

---

## 📊 ¿Por qué es útil este setup?

- Experimentar con **HDFS** (almacenamiento distribuido) y **YARN** (planificación/recursos) en local.
- Ejecutar **DAGs de Airflow** que interactúen con Hadoop.
- Ingerir/transformar datos con **NiFi** y escribir resultados en **HDFS**.
- Aprender y probar conceptos Big Data sin montar infraestructura cloud.

---

## 🧯 Troubleshooting rápido

- Ver logs de un servicio:
```bash
docker logs -n 200 <nombre_contenedor>
```

- Ver estado de contenedores:
```bash
docker ps
```

- Reiniciar un servicio:
```bash
docker restart <nombre_contenedor>
```

- Bajar y limpiar (incluyendo volúmenes del proyecto):
```bash
docker compose down -v
```

---

## ✅ Conclusión

Con un solo `docker compose up -d` tienes un **mini data platform** en local: **Hadoop (HDFS + YARN)** + **Airflow** + **NiFi** integrados para probar pipelines, orquestación e ingesta de datos de forma rápida.

---
