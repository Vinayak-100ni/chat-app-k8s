# 📊 Monitoring Setup with Prometheus & Grafana using Helm

This guide explains how to set up **Prometheus** and **Grafana** on a Kubernetes cluster using the official [Prometheus Community Helm Charts](https://prometheus-community.github.io/helm-charts).  
After setup, we will deploy a **Chat Application** and monitor it with Prometheus & Grafana.

---

## 🚀 Prerequisites

- Kubernetes cluster (Minikube, Kind, EKS, GKE, or AKS)
- kubectl installed and configured
- Helm v3 installed

---

## ⚙️ Install Helm

```bash
curl -fsSL -o get_helm.sh https://raw.githubusercontent.com/helm/helm/main/scripts/get-helm-3
chmod 700 get_helm.sh
./get_helm.sh
Verify installation:

bash
Copy code
helm version
📦 Create Monitoring Namespace
bash
Copy code
kubectl create namespace monitoring
📥 Add Prometheus Community Helm Repo
bash
Copy code
helm repo add prometheus-community https://prometheus-community.github.io/helm-charts
helm repo update
📊 Install Prometheus & Grafana
bash
Copy code
helm install prometheus-stack prometheus-community/kube-prometheus-stack \
  --namespace monitoring \
  --set prometheus.service.nodePort=30000 \
  --set grafana.service.nodePort=31000 \
  --set grafana.service.type=NodePort \
  --set prometheus.service.type=NodePort
🔎 Access Prometheus & Grafana
Get All Services
bash
Copy code
kubectl get svc -n monitoring
Expose Prometheus
bash
Copy code
kubectl port-forward svc/prometheus-stack-kube-prom-prometheus 9090:9090 -n monitoring --address=0.0.0.0 &
👉 Access Prometheus at: http://localhost:9090

Expose Grafana
bash
Copy code
kubectl port-forward svc/prometheus-stack-grafana 3000:80 -n monitoring --address=0.0.0.0 &
👉 Access Grafana at: http://localhost:3000

🔑 Grafana Login
Get the default username and password:

bash
Copy code
# Username
echo "admin"

# Password
kubectl get secret prometheus-stack-grafana -n monitoring -o jsonpath="{.data.admin-password}" | base64 --decode
💬 Deploy Chat Application
After Prometheus & Grafana are running, deploy Chat App into the cluster.
Prometheus will scrape metrics, and Grafana will help visualize CPU, memory, and performance dashboards.

📈 Monitoring Dashboard
Prometheus → Query metrics directly

Grafana → Visualize metrics with dashboards (Kubernetes cluster health, resource usage, app performance, etc.)

✅ Summary
Installed Helm

Created monitoring namespace

Installed Prometheus + Grafana using Helm

Exposed services for access

Retrieved Grafana credentials

Ready to monitor Chat App metrics

Deploy Chat App

Add custom Grafana dashboards

Configure alerts with Prometheus Alertmanager
