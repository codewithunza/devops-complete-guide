This text provides a detailed explanation of installing and configuring Prometheus and Grafana on a Kubernetes cluster using Helm. Each concept and command used is explained in detail below, along with explanations of their purposes and the expected outputs.

### 1. **Exposing Prometheus Server**
   - **Command:**
 ```bash
     kubectl expose service prometheus-server --type=NodePort --name=prometheus-server-ext
 ```
   - **Explanation:** 
     By default, the Prometheus server is exposed using a `ClusterIP` service, which means it is only accessible within the cluster. The above command converts it to a `NodePort` service, which makes it accessible from outside the cluster on a specific port. 
   - **Purpose:** This allows external access to the Prometheus dashboard through a browser by opening it on the node’s IP and specified port.
   - **Expected Output:** After running `kubectl get svc`, you should see a new service named `prometheus-server-ext` with an assigned `NodePort`.

### 2. **Retrieving Kubernetes Node IP**
   - **Command:**
 ```bash
     minikube ip
 ```
   - **Explanation:** 
     This command returns the IP address of the Minikube Kubernetes node.
   - **Purpose:** This IP address, along with the NodePort, is used to access the Prometheus server externally through a browser.
   - **Expected Output:** The output will be the IP address of the Minikube node, e.g., `192.168.99.100`.

### 3. **Accessing Prometheus Server in Browser**
   - **URL:**
 ```bash
     http://<MINIKUBE_IP>:<NODE_PORT>
 ```
     Example:
 ```bash
     http://192.168.99.100:31110
 ```
   - **Explanation:** 
     After exposing the Prometheus server, you can access the Prometheus UI by entering the node’s IP and the port in your browser.
   - **Purpose:** This allows you to view and query Prometheus metrics via the UI.
   - **Expected Output:** The Prometheus dashboard will be displayed, allowing you to run queries and visualize Kubernetes cluster metrics.

### 4. **Querying Prometheus Metrics**
   - **Explanation:**
     Prometheus allows users to write queries to extract specific metrics. For instance, you can retrieve CPU usage, memory consumption, or pod statuses.
   - **Purpose:** These queries provide detailed insights into the health and performance of the Kubernetes cluster.
   - **Example Query:**
 ```promql
     up
 ```
     This query returns the status of all monitored services in the cluster.

### 5. **Using Custom Metrics in Prometheus**
   - **Explanation:**
     By default, Prometheus can only scrape metrics from the Kubernetes API server or `kube-state-metrics`. To monitor custom applications, developers need to write custom metric endpoints using Prometheus libraries.
   - **Purpose:** This allows monitoring of custom applications deployed in Kubernetes clusters by exposing specific metrics like health checks or request rates.

### 6. **Installing Grafana using Helm**
   - **Command:**
 ```bash
     helm repo add grafana https://grafana.github.io/helm-charts
     helm repo update
     helm install grafana grafana/grafana
 ```
   - **Explanation:**
     Grafana is a visualization tool for metrics, and it integrates with Prometheus to visualize the metrics it collects. The commands above add the Grafana Helm repository, update the Helm repo, and install Grafana.
   - **Purpose:** Helm simplifies the installation of Grafana on Kubernetes.
   - **Expected Output:** Grafana will be installed, and its services can be verified using `kubectl get pods`.

### 7. **Retrieving Grafana Admin Password**
   - **Command:**
 ```bash
     kubectl get secret --namespace default grafana -o jsonpath="{.data.admin-password}" | base64 --decode
 ```
   - **Explanation:**
     After Grafana installation, this command retrieves the admin password required to log in.
   - **Purpose:** To access the Grafana dashboard, you need the admin credentials, and this command provides them.
   - **Expected Output:** The decoded password will be shown in the terminal.

### 8. **Exposing Grafana Service**
   - **Command:**
 ```bash
     kubectl expose service grafana --type=NodePort --name=grafana-ext
 ```
   - **Explanation:**
     Similar to Prometheus, the Grafana service is initially exposed using `ClusterIP`. The above command changes it to `NodePort` for external access.
   - **Purpose:** This allows users to access Grafana from outside the cluster.
   - **Expected Output:** A new service `grafana-ext` will be created and accessible via a NodePort.

### 9. **Accessing Grafana Dashboard**
   - **URL:**
 ```bash
     http://<MINIKUBE_IP>:<NODE_PORT>
 ```
     Example:
 ```bash
     http://192.168.99.100:31281
 ```
   - **Explanation:** 
     After exposing Grafana, you can access it using the node’s IP and port, similar to Prometheus.
   - **Purpose:** To visualize the Kubernetes metrics in Grafana’s dashboard.
   - **Expected Output:** The Grafana login page will be displayed, where you can use the retrieved password to log in.

### 10. **Kubernetes `kube-state-metrics`**
   - **Explanation:** 
     `kube-state-metrics` is a Kubernetes component that provides detailed metrics beyond what the API server provides, such as pod counts, deployment status, and replica states.
   - **Purpose:** It is essential for collecting more granular metrics about Kubernetes resources, such as how many replicas of a deployment are running versus the desired count.
   - **Usage:** By installing `kube-state-metrics`, you can monitor the health of Kubernetes objects more thoroughly.

### Summary:
- **Prometheus:** A powerful open-source tool for monitoring and alerting based on metrics, particularly useful for Kubernetes clusters.
- **Grafana:** A visualization tool that works with Prometheus to display metrics in a user-friendly dashboard.
- **Helm:** A package manager for Kubernetes that simplifies the installation of applications like Prometheus and Grafana.
- **Custom Metrics:** Developers can expose additional metrics for custom applications in Kubernetes.
- **kube-state-metrics:** A tool that provides detailed metrics on Kubernetes objects.

These steps help monitor and visualize the performance of Kubernetes clusters, making it easier to ensure they run efficiently.