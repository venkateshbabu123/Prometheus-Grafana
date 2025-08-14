# Prometheus-Grafana
Setup the Prometheus + Grafana in local minikube
Step 1: Start Minikube
  minikube start
  
Step 2: Add the Helm repos for Prometheus & Grafana
helm repo add prometheus-community https://prometheus-community.github.io/helm-charts
helm repo update

Step 3: Create a monitoring namespace
kubectl create namespace monitoring

Step 4: Install kube-prometheus-stack
helm install monitoring prometheus-community/kube-prometheus-stack --namespace monitoring

This will:
   •	Deploy Prometheus
   •	Deploy Grafana
   •	Deploy Alertmanager
   •	Set up default dashboards & service monitors

Step 5: Access Grafana 
By default, Grafana is not exposed outside the cluster, so we’ll port-forward it.
kubectl --namespace monitoring port-forward svc/monitoring-grafana 3000:80
    Now open Grafana in your browser:

   Open the browser in local : 
   http://localhost:3000

Step 6: Get Grafana login credentials

kubectl get secret --namespace monitoring monitoring-grafana \
  -o jsonpath="{.data.admin-user}" | base64 --decode; echo
kubectl get secret --namespace monitoring monitoring-grafana \
  -o jsonpath="{.data.admin-password}" | base64 --decode; echo
      Default:
        •	Username: admin
        •	Password: (from above command)

Step 7: Access Prometheus
kubectl --namespace monitoring port-forward svc/monitoring-kube-prometheus-prometheus 9090:9090

    Open the browser for access

    http://localhost:9090

Step 8 : (Optional step) Persist Data

helm upgrade monitoring prometheus-community/kube-prometheus-stack   --namespace monitoring \
  --set prometheus.prometheusSpec.storageSpec.volumeClaimTemplate.spec.resources.requests.storage=10Gi \
  --set grafana.persistence.enabled=true \
  --set grafana.persistence.size=5Gi

