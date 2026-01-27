# **Kubernetes Deployment & Management – A Practical Guide 🚀**

> **TL;DR:** Deploy microservices on Kubernetes with Redis, RabbitMQ, Elasticsearch, and SQL Server. Use Terraform for IaC and ELK for monitoring. Pin container versions in production. | ⏱️ 8 min read

---

## **Introduction**

Kubernetes (K8s) is the industry standard for **container orchestration**, allowing applications to scale, self-heal, and manage deployments efficiently. This guide outlines how to deploy a **microservices-based architecture** using **Kubernetes**, with a focus on **common services, monitoring, infrastructure as code (IaC) with Terraform, and integration testing**.

---

## **🛠 Kubernetes Setup & Deployment**

In a **staging environment**, a **single-node Kubernetes cluster** managed with **kubectl** can host:

- **Core Services**: Redis, RabbitMQ, Elasticsearch, SQL Server  
- **Monitoring Stack**: ELK (Elasticsearch, Logstash, Kibana)  
- **Infrastructure as Code (IaC)**: Managed using **Terraform**  
- **Microservices**: Independent services interacting with these shared resources  

📌 **Deployment Strategy**: Each microservice runs as a **Kubernetes Deployment**, with independent scaling capabilities.

---

## **📦 Deploying Core Services in Kubernetes**

These **stateful services** are essential for microservices communication, caching, and data persistence.

### **1️⃣ Redis (In-Memory Caching)**

**Used for:** Distributed caching, session storage, and real-time data.

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: redis
spec:
  replicas: 1
  selector:
    matchLabels:
      app: redis
  template:
    metadata:
      labels:
        app: redis
    spec:
      containers:
      - name: redis
        image: redis:7.4-alpine  # Pin version in production
        ports:
        - containerPort: 6379
```

✅ **Persistent Storage:**  
For persistent caching, use a **Redis StatefulSet** instead of Deployment.

---

### **2️⃣ RabbitMQ (Message Broker)**

**Used for:** Event-driven architecture, asynchronous processing, and inter-service communication.

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: rabbitmq
spec:
  replicas: 1
  selector:
    matchLabels:
      app: rabbitmq
  template:
    metadata:
      labels:
        app: rabbitmq
    spec:
      containers:
      - name: rabbitmq
        image: rabbitmq:4.0-management-alpine
        ports:
        - containerPort: 5672   # Messaging
        - containerPort: 15672  # Management UI
```

✅ **Scaling Consideration:**  
For **high availability**, RabbitMQ should be **clustered across multiple nodes**.

---

### **3️⃣ SQL Server (Relational Database)**

**Used for:** Storing structured data, transactions, and business logic.

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: sqlserver
spec:
  replicas: 1
  selector:
    matchLabels:
      app: sqlserver
  template:
    metadata:
      labels:
        app: sqlserver
    spec:
      containers:
      - name: sqlserver
        image: mcr.microsoft.com/mssql/server:2022-latest
        env:
        - name: SA_PASSWORD
          valueFrom:
            secretKeyRef:
              name: sqlserver-secret
              key: password
        - name: ACCEPT_EULA
          value: "Y"
        ports:
        - containerPort: 1433
```

✅ **Data Persistence:**  
Mount a **Persistent Volume Claim (PVC)** to prevent data loss.

⚠️ **Security Note:**  
Never hardcode passwords—use Kubernetes Secrets as shown above.

---

### **4️⃣ Elasticsearch (Search & Logging)**

**Used for:** Full-text search, analytics, and log storage.

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: elasticsearch
spec:
  replicas: 1
  selector:
    matchLabels:
      app: elasticsearch
  template:
    metadata:
      labels:
        app: elasticsearch
    spec:
      containers:
      - name: elasticsearch
        image: elasticsearch:8.17.0
        env:
        - name: discovery.type
          value: "single-node"
        - name: xpack.security.enabled
          value: "true"
        ports:
        - containerPort: 9200
```

✅ **Security Note:**  
Use **x-pack security** to secure Elasticsearch deployments in production.

---

## **📊 Monitoring with ELK**

**Stack Overview:**  
- **Elasticsearch**: Stores logs.  
- **Logstash**: Parses & transforms logs.  
- **Kibana**: Visualizes logs.

**Why ELK?**  
- Tracks **microservices logs** across Kubernetes pods.  
- Detects **failures & anomalies** in real time.  
- Provides **insights into performance bottlenecks**.

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: kibana
spec:
  replicas: 1
  selector:
    matchLabels:
      app: kibana
  template:
    metadata:
      labels:
        app: kibana
    spec:
      containers:
      - name: kibana
        image: kibana:8.17.0
        env:
        - name: ELASTICSEARCH_HOSTS
          value: "http://elasticsearch:9200"
        ports:
        - containerPort: 5601
```

---

## **🔧 Integration Testing with WireMock**

**Simulating External Dependencies:**  
For integration testing, use **WireMock** to simulate external services like RabbitMQ, Redis, SQL Server.

✅ **Benefits of WireMock:**  
- Eliminates dependency on real systems.  
- Provides **mock responses** for APIs or events.  
- Enables **fast, isolated testing** without infrastructure costs.

---

## **📜 Infrastructure as Code with Terraform**

**Why Terraform?**  
- Manages Kubernetes infrastructure declaratively.  
- Enables consistent environments across **staging** and **production**.

```hcl
provider "kubernetes" {
  config_path = "~/.kube/config"
}

resource "kubernetes_deployment" "redis" {
  metadata {
    name = "redis"
    labels = {
      app = "redis"
    }
  }

  spec {
    replicas = 1

    selector {
      match_labels = {
        app = "redis"
      }
    }

    template {
      metadata {
        labels = {
          app = "redis"
        }
      }

      spec {
        container {
          image = "redis:7.4-alpine"
          name  = "redis"

          port {
            container_port = 6379
          }
        }
      }
    }
  }
}
```

---

## **🚀 Stay Connected**

🔗 **Learn More:** [Cycolis Software](https://cycolis-software.ro/home)  
💻 **Explore Our Work:** [GitHub](https://github.com/Cycolis-Software)  
💼 **Connect on LinkedIn:** [LinkedIn](https://www.linkedin.com/company/cycolis-software)  
🐦 **Follow for Updates:** [Twitter](https://x.com/CycolisSoftware)
