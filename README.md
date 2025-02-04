# Promtail-loki-grafana-logs-aggregation
Loki, Promtail, and Grafana on a Kind Cluster (VirtualBox)

This document provides step-by-step instructions to set up and deploy Loki, Promtail, and Grafana on a Kind cluster running inside VirtualBox.

Prerequisites

Ensure you have the following installed:

Docker (configured with enough CPU & RAM)

Kubectl

Kind

Helm

value reference file for loki-stack
https://github.com/grafana/helm-charts/blob/main/charts/loki-stack/values.yaml


1️⃣ Create a Kind Cluster

Run the following command to create a Kind cluster:

```shell

kind create cluster --image kindest/node:v1.32.0@sha256:c48c62eac5da28cdadcf560d1d8616cfa6783b58f0d94cf63ad1bf49600cb027 --name pk --config config.yml


```

2️⃣ Install Helm and Add the Grafana Repo

helm repo add grafana https://grafana.github.io/helm-charts
helm repo update

3️⃣ Deploy Loki Using Helm

Create a loki.yml configuration file as needed and install Loki:
loki:
  enabled: true

promtail:
  enabled: true

grafana:
  enabled: true
  service:
    type: NodePort

helm install loki grafana/loki-stack --values loki.yml

4️⃣ Verify Deployment

Check all running pods and services:

kubectl get all

5️⃣ Retrieve Grafana Admin Password

kubectl get secret --namespace default loki-grafana -o jsonpath="{.data.admin-password}" | base64 --decode ; echo

Save the password for future use.

6️⃣ Expose Grafana Service

By default, Grafana runs inside the cluster. To access it externally, patch the service:

kubectl patch svc grafana -p '{"spec": {"type": "NodePort"}}'

You can now access Grafana using your node's IP and the assigned NodePort.

7️⃣ Apply Kubernetes Configurations

If you have additional Kubernetes configurations, apply them:

kubectl apply -f .

8️⃣ Access Grafana

To access Grafana, retrieve the NodePort and navigate to:

kubectl get svc grafana

Open your browser and go to:

http://<node-ip>:<grafana-nodeport>

Login credentials:

Username: admin

Password: (Use the retrieved password)

9️⃣ Configure Loki as a Data Source in Grafana

Login to Grafana.

Navigate to Configuration → Data Sources.

Add a new data source.

Choose Loki and set the URL to http://loki:3100.

Click Save & Test.

🔍 Viewing Logs in Grafana

Once Loki is set up:

Go to Explore.

Select Loki as the data source.

Use {job="loki"} to query logs.

Dashboard creation
id for dashboard

15141

Your Loki, Promtail, and Grafana stack is now set up and ready to use on your Kind cluster! 🚀

