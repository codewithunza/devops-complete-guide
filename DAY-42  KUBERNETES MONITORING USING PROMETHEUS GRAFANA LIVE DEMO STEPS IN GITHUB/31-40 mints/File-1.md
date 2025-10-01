### **Extracted Concepts: Understanding Cube State Metrics, Prometheus Configurations, and Application Monitoring with Prometheus**

This section elaborates on how to visualize metrics using Cube State Metrics, configure Prometheus for scraping additional endpoints, and set up application-specific monitoring in a Kubernetes cluster.

---

### **1. Viewing Cube State Metrics**

#### **Concept**:
**Cube State Metrics** provides additional metrics for Kubernetes resources beyond what the Kubernetes API exposes. These metrics are accessible via the `/metrics` endpoint in a **plain text format**, showing data about **deployments, services, nodes, etc.**.

#### **Steps**:
1. **Access Cube State Metrics**: 
   - Use the IP address and port exposed from the Kubernetes service (NodePort). For example:
 ```bash
   http://192.168.64.15:30421/metrics
 ```
   - **Output**: It shows a large set of metrics in **Prometheus' metrics format**, which includes details like deployment statuses, replica counts, and pod information.

2. **Example Query**:
   - If you want to know the status of deployments, use Prometheus queries like:
 ```plaintext
   kube_deployment_status_replicas
 ```
   - **Purpose**: This query returns information about the replica status of each deployment.

3. **Visualization in Grafana**:
   - Prometheus provides the raw metrics, but **Grafana** converts these into **visual representations** such as graphs and charts, making it easier to monitor the health and performance of Kubernetes clusters.

#### **Scenario**:
- You can visualize metrics in **Grafana** for Kubernetes clusters using predefined dashboards. The Cube State Metrics endpoint provides detailed deployment and resource-level insights. These metrics can be used to monitor **replicas, services, and deployment statuses**, aiding in understanding cluster health.

---

### **2. Adding Cube State Metrics to Prometheus Scrape Configurations**

#### **Concept**:
Prometheus gathers data from various endpoints by scraping metrics from services. To monitor Cube State Metrics, you need to add its endpoint to the **Prometheus configuration file** so that Prometheus can scrape this data.

#### **Steps**:
1. **View Prometheus ConfigMap**:
   - Use the following command to view the **Prometheus ConfigMap**:
 ```bash
   kubectl edit cm prometheus-server
 ```
   - Inside this ConfigMap, you'll find **scrape_configs** where Prometheus is configured to scrape different metrics.

2. **Add Cube State Metrics**:
   - You need to add the Cube State Metrics endpoint in the scrape configuration. For example:
 ```yaml
   - job_name: 'kube-state-metrics'
     static_configs:
       - targets: ['192.168.64.15:30421']
 ```
   - **Purpose**: This tells Prometheus to scrape metrics from the Cube State Metrics service, allowing it to retrieve more detailed Kubernetes metrics.

#### **Scenario**:
After adding this configuration, Prometheus will automatically begin scraping metrics from Cube State Metrics, enabling it to collect **detailed deployment information** such as **replica counts** and **pod statuses**. This configuration allows for better monitoring of resource health within the cluster.

---

### **3. Monitoring Custom Applications with Prometheus**

#### **Concept**:
In addition to monitoring Kubernetes resources, you can use Prometheus to monitor custom applications. For this, developers must expose **custom metrics** from their applications, which Prometheus can scrape and monitor.

#### **Steps**:
1. **Expose Custom Metrics**:
   - Developers need to integrate **Prometheus client libraries** (available in **Go**, **Python**, **Java**, etc.) into their applications.
   - The application should expose a `/metrics` endpoint that Prometheus can scrape. This endpoint would expose relevant application metrics like **response times**, **request rates**, and **error rates**.
   - **Example**: If a Python application exposes the following metrics:
 ```plaintext
     http_requests_total{method="GET",code="200"} 1027
 ```

2. **Update Prometheus Scrape Config**:
   - Add the application’s `/metrics` endpoint to the Prometheus ConfigMap, similarly to how Cube State Metrics was added:
 ```yaml
   - job_name: 'my-app'
     static_configs:
       - targets: ['app-service:8080']
 ```
   - **Purpose**: This configuration tells Prometheus to scrape the custom application’s metrics.

3. **Monitor Custom Metrics in Grafana**:
   - You can visualize these custom metrics in **Grafana** by creating custom dashboards based on the metrics exposed by the application.

#### **Scenario**:
If your organization runs custom applications on Kubernetes, you can monitor the application's **performance and health** (e.g., latency, error rates) using Prometheus. You need to ask developers to expose the necessary metrics, which Prometheus will then scrape and store for visualization in Grafana.

---

### **4. Real-Time Monitoring and Visualization with Prometheus and Grafana**

#### **Concept**:
Once Prometheus and Grafana are set up, you can continuously monitor your Kubernetes cluster’s health in **real time**. This involves viewing metrics like **node statuses**, **API server uptime**, **pod health**, and **application metrics**.

#### **Steps to Access Real-Time Metrics**:
1. **Access Prometheus**:
   - Use Prometheus' built-in UI to run **PromQL queries** for specific metrics (e.g., deployment status, node uptime). For example:
 ```promql
   up{job="kube-state-metrics"}
 ```
   - **Purpose**: This query checks the availability of the Cube State Metrics service.

2. **Access Grafana**:
   - Use Grafana to visualize metrics in a real-time dashboard. You can create custom dashboards using the collected metrics or use predefined dashboards for Kubernetes monitoring.
   - Example: A dashboard showing **node uptime**, **CPU usage**, and **memory consumption**.

#### **Scenario**:
With this setup, you can keep an eye on your cluster in **real time**, ensuring that your deployments, services, and nodes are healthy. Grafana’s visualization capabilities help you detect issues early, while Prometheus’ alerting system can notify you when something goes wrong (e.g., a node going down or a service becoming unavailable).

---

### **Summary of Commands and Scenarios**

1. **Access Cube State Metrics**:
   - Command: 
 ```bash
     http://<minikube-ip>:30421/metrics
 ```
   - **Scenario**: View detailed resource-level metrics for your Kubernetes cluster.

2. **Add Cube State Metrics to Prometheus**:
   - Command:
 ```yaml
     - job_name: 'kube-state-metrics'
       static_configs:
         - targets: ['<minikube-ip>:30421']
 ```
   - **Scenario**: Configure Prometheus to scrape Cube State Metrics for deeper Kubernetes resource insights.

3. **Expose Custom Application Metrics**:
   - **Example**: Expose a `/metrics` endpoint in your application and add it to the Prometheus scrape config.
   - **Scenario**: Monitor custom application health by exposing metrics through Prometheus.

4. **Run a PromQL Query**:
   - Command:
 ```promql
     kube_deployment_status_replicas
 ```
   - **Scenario**: Retrieve deployment replica information from Prometheus.

---

### **Conclusion**

In this section, we explored how to view and integrate **Cube State Metrics** for detailed Kubernetes monitoring, configure **Prometheus** to scrape new endpoints, and monitor **custom applications** by exposing their metrics. These steps enhance visibility into your cluster and applications, ensuring you have a comprehensive understanding of both Kubernetes resources and custom application health.

--------------------------------------------------------------------------------------------------------------------------------
# OR
--------------------------------------------------------------------------------------------------------------------------------

### Extracted Concepts: Monitoring Kubernetes with Prometheus, Grafana, and Cube State Metrics

---

### **1. Accessing Cube State Metrics**

#### **Scenario:**
After exposing **Cube State Metrics**, you can access the metrics provided by Cube State Metrics through a **NodePort**. This exposes metrics beyond what Kubernetes provides by default, such as deployment statuses, pod states, and more.

#### **Access Cube State Metrics:**
1. Use your browser to access the Cube State Metrics endpoint. The endpoint looks like this:
   
```plaintext
http://<minikube-ip>:30421/metrics
```

2. When you navigate to this URL, you'll see a list of metrics in **Prometheus format** (key-value pairs). This raw data gives you detailed insights into your Kubernetes objects, including the status of deployments, pods, and services.

#### **Purpose:**
Cube State Metrics exposes additional information beyond the Kubernetes API, such as the status of replicas, readiness of pods, and more. These metrics are valuable for advanced monitoring and troubleshooting.

---

### **2. Using Prometheus Queries to Retrieve Metrics**

#### **Scenario:**
Once Cube State Metrics is exposed, you can execute Prometheus queries to get specific data. This raw data can be used directly in Prometheus or visualized in Grafana.

#### **Example Query for Deployment Status:**

```plaintext
up{job="kubernetes-deployments"}
```

- This query fetches the status of Kubernetes deployments.
- You can execute this query in **Prometheus** and see the raw metrics.
  
#### **Command:**

```bash
kubectl exec -it <prometheus-pod> -- curl http://<minikube-ip>:30421/metrics
```

This allows you to retrieve the data within the cluster itself.

#### **Purpose:**
Running queries like these helps you extract valuable metrics, such as the health of deployments, from Prometheus. These metrics are essential for tracking the state of applications and services running in your Kubernetes cluster.

---

### **3. Using Grafana for Visualization**

#### **Scenario:**
Grafana transforms the raw data from Prometheus (retrieved by Prometheus queries) into visually appealing dashboards. You can integrate Cube State Metrics with Grafana to create a dashboard that visualizes metrics such as pod status, deployment replicas, and node health.

#### **Steps to Create a Grafana Dashboard:**
1. After setting Prometheus as a **data source**, go to **Dashboards** in Grafana.
2. Use **Dashboard ID: 3662** to import a pre-built Kubernetes monitoring dashboard.

```bash
Dashboard ID: 3662
```

This dashboard will automatically generate visualizations for various Kubernetes metrics, including node uptime, API server health, and replica statuses.

#### **Purpose:**
Grafana provides a **visual interface** for interpreting metrics. Instead of reading raw Prometheus data, Grafana displays it in graphs, making it easier for DevOps engineers to monitor the health of Kubernetes clusters and applications.

---

### **4. Adding Custom Metrics in Prometheus**

#### **Scenario:**
By default, Prometheus scrapes metrics from the Kubernetes API and Cube State Metrics. However, if you want to monitor **custom applications** deployed by your developers, you'll need to add their metrics endpoints to Prometheus.

#### **Steps to Add Custom Metrics:**
1. **Edit the Prometheus ConfigMap** to scrape new metrics.

```bash
kubectl edit configmap prometheus-server -n default
```

2. In the `prometheus.yaml` file inside the ConfigMap, you will see **scrape_configs**. Add a new job configuration for Cube State Metrics or any custom application.

Example:

```yaml
scrape_configs:
  - job_name: 'custom-app'
    static_configs:
    - targets: ['<custom-app-ip>:<port>']
```

3. Apply the updated ConfigMap.

#### **Purpose:**
This allows Prometheus to scrape metrics from custom applications. For example, if your developers expose an endpoint `/metrics` from their applications, Prometheus can collect and store this data.

---

### **5. Writing a Prometheus Metric Server**

#### **Scenario:**
To monitor **custom applications** in Kubernetes, developers need to expose metrics using Prometheus libraries. These metrics can be related to application health, response times, error rates, and more.

#### **How Developers Can Write a Prometheus Metric Server:**
- **Prometheus Libraries**: Prometheus offers client libraries in multiple languages (Go, Python, Java) that developers can use to expose application metrics.

Example (Python):

```python
from prometheus_client import start_http_server, Counter

# Define a metric
request_count = Counter('app_requests_total', 'Total number of requests')

# Start the metrics server
if __name__ == '__main__':
    start_http_server(8000)

# Increment the counter in the application logic
def handle_request():
    request_count.inc()
```

The application exposes metrics on `/metrics` which can be scraped by Prometheus.

#### **Purpose:**
Exposing custom application metrics allows Prometheus to monitor not just the Kubernetes infrastructure but also the health and performance of the applications running inside Kubernetes.

---

### **6. Configuring Prometheus to Scrape Custom Metrics**

#### **Scenario:**
After your developers have exposed the `/metrics` endpoint of their application, you need to configure Prometheus to scrape these metrics.

#### **Steps:**
1. **Edit Prometheus ConfigMap**:

```bash
kubectl edit configmap prometheus-server
```

2. Add a new job under **scrape_configs** for the custom application:

```yaml
scrape_configs:
  - job_name: 'custom-application'
    static_configs:
    - targets: ['app-service:8000']
```

3. Save the configuration and restart Prometheus.

#### **Purpose:**
This configuration allows Prometheus to start collecting metrics from the custom application and include it in your monitoring system.

---

### **7. Conclusion: The Role of Prometheus, Grafana, and Cube State Metrics**

- **Prometheus** acts as the **metrics storage and query engine**. It scrapes metrics from Kubernetes, Cube State Metrics, and custom application endpoints.
- **Grafana** provides **visualization** for Prometheus data, making it easier for DevOps engineers to track the health of their clusters and applications.
- **Cube State Metrics** adds value by providing detailed metrics about Kubernetes objects, such as deployments, services, and pod statuses, which are not available through default Kubernetes metrics.

These tools together form a comprehensive **monitoring solution** for Kubernetes, enabling deep visibility into both infrastructure and application health.