# GPU-Util-Dashboard

Drop-in Helm chart that bundles Prometheus + Grafana and a ready-made NVIDIA DCGM dashboard.

Works
* **Real GPUs** – discovers the `nvidia-dcgm-exporter` DaemonSet that the official GPU-Operator deploys.
* **No GPUs** – optional fake-GPU generator included for demo / CI environments.

---
## 1  Prerequisites
* Kubernetes ≥ 1.25 & `kubectl`
* Helm ≥ 3.10
* For real GPUs – the [NVIDIA GPU-Operator](https://github.com/NVIDIA/gpu-operator) already running

```bash
# add repos once
helm repo add prometheus-community https://prometheus-community.github.io/helm-charts
helm repo add grafana              https://grafana.github.io/helm-charts
helm repo update
```

---
## 2  Quick install (real GPUs)
```bash
helm install gpu-dash . \
  --create-namespace -n gpu-monitoring
```
Access Grafana
```bash
kubectl -n gpu-monitoring port-forward svc/gpu-dash-grafana 3000:80
# → http://localhost:3000  (admin / admin)
```

---
## 3  Quick install (no GPUs)
```bash
helm install gpu-dash . \
  --create-namespace -n gpu-monitoring \
  --set fakeGpu.enabled=true
```
A tiny Python pod emits realistic DCGM metrics so the dashboard comes alive.

---
## 4  Customisation
* `dashboard.gpuPricePerHour` – USD cost shown in the *GPU dollars burned* panel (default `3.90`).
* Any other Grafana/Prometheus setting can be overridden with `--set` or a custom `values.yaml`.

---
## 5  Upgrade / uninstall
```bash
helm upgrade  gpu-dash . -n gpu-monitoring   # upgrade
helm uninstall gpu-dash   -n gpu-monitoring   # delete everything
```

---
### Layout
```
Chart.yaml
values.yaml
charts/                 # Helm deps (auto-generated)
templates/
  fake-gpu-metrics.yaml
  fixed-nvidia-dashboard.yaml
  dashboards.tpl
  price-dashboard.yaml
  test-dashboard.yaml
```

---
Happy monitoring 🚀 