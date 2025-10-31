SonarQube + PostgreSQL Docker Setup

A production-grade Docker Compose stack for deploying SonarQube (Community Edition) with PostgreSQL 17 as the backend database.

This setup is designed for DevOps, SRE, and development teams to host a self-contained code quality and static analysis platform that persists data across restarts, supports health checks, and runs reliably in production environments.

🚀 Features

SonarQube (Community Edition) — code quality and security analysis

PostgreSQL 17 — reliable and performant database backend

Persistent volumes for database and SonarQube data

Automatic restarts for resilience

Health checks to ensure PostgreSQL readiness before SonarQube starts

Optimized for production — minimal manual setup

Exposes SonarQube on port 55000

🧩 Architecture Overview
+--------------------+       +--------------------+
|    SonarQube       | <---> |   PostgreSQL 17    |
|  Port: 55000       |       |   Port: 5432       |
|  /opt/sonarqube    |       |  /var/lib/postgresql|
+--------------------+       +--------------------+

         |
         |  Docker Network: sonar-net
         |
   +-----------------------------+
   | Docker Volumes (persistent) |
   +-----------------------------+
            |
            |  - sonarqube_data
            |  - postgres_data

⚙️ Prerequisites

| Requirement    | Minimum Version    | Description                    |
| -------------- | ------------------ | ------------------------------ |
| Docker         | 20.x or higher     | Container runtime              |
| Docker Compose | 1.29+ / Compose V2 | For orchestration              |
| System Memory  | 4 GB+              | SonarQube needs ~2GB minimum   |
| CPU            | 2 Cores+           | Recommended                    |
| Disk Space     | 10 GB+             | For database and analysis data |

📦 Folder Structure
sonarqube-docker/
├── docker-compose.yaml      # Docker stack definition
├── README.md                # Documentation
└── (Volumes created externally)

🔧 Setup Instructions

🧱 Create External Volumes (First Time Only)

These volumes persist your data and should be created once before deployment:

docker volume create sonarqube-data
docker volume create sonarqube-extensions
docker volume create sonarqube-logs
docker volume create sonarqube-db
docker volume create sonarqube-db-data

▶️ Deployment

1️⃣ Start the stack

```
docker-compose up -d
```

2️⃣ Verify containers are running

```
docker ps
```

You should see both:
```
sonarqube
postgres
```

3️⃣ Monitor logs

```
docker-compose logs -f sonarqube
```

Wait until you see:

```
SonarQube is up
```

🌐 Access the SonarQube Dashboard

Open your browser:

👉 http://localhost:55000

or
👉 http://<your-server-ip>:55000

🔐 Default Credentials

| Service      | Username | Password     |
| ------------ | -------- | ------------ |
| SonarQube UI | `admin`  | `admin`      |
| PostgreSQL   | `sonar`  | `1MQh41gK#>` |


⚠️ On first login, you’ll be prompted to change the default SonarQube admin password.

⚙️ Environment Variables Summary

| Variable              | Description            | Default                             |
| --------------------- | ---------------------- | ----------------------------------- |
| `POSTGRES_USER`       | DB username            | sonar                               |
| `POSTGRES_PASSWORD`   | DB password            | 1MQh41gK#>                          |
| `POSTGRES_DB`         | DB name                | sonarqube                           |
| `SONAR_JDBC_URL`      | JDBC connection string | jdbc:postgresql://db:5432/sonarqube |
| `SONAR_JDBC_USERNAME` | SonarQube DB user      | sonar                               |
| `SONAR_JDBC_PASSWORD` | SonarQube DB password  | 1MQh41gK#>                          |

Maintenance & Troubleshooting
- To stop the stack:

  ```
  docker-compose down
  ```
- To view logs:

  ```
  docker-compose logs -f sonarqube
  docker-compose logs -f postgres
  ```

- To back up data, back up the Docker volumes:

```
docker exec -t postgres pg_dumpall -c -U sonar > backup_$(date +%F).sql
```
Restore from backup:

```
cat backup_<date>.sql | docker exec -i postgres psql -U sonar
```


