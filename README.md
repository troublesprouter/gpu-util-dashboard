# GPU-Util-Dashboard

Drop-in Helm chart that provisions Prometheus + Grafana together with a ready-made NVIDIA DCGM dashboard.
Works:
* on **any NVIDIA/GPU cluster** – metrics come from `nvidia-dcgm-exporter` that the official GPU-Operator already deploys.
* on **laptops / CI** with **no GPUs at all** – just add the optional *fake-GPU* generator shipped in the chart (handy for testing).

---
## 1. Prerequisites

* Kubernetes ≥1.25 & `kubectl`
* Helm ≥3.10
* For real GPUs – the [NVIDIA GPU Operator](https://github.com/NVIDIA/gpu-operator) already running

```bash
# add Helm repos once
helm repo add prometheus-community https://prometheus-community.github.io/helm-charts
helm repo add grafana            https://grafana.github.io/helm-charts
helm repo update
```

---
## 2. Quick install (real GPUs)
```bash
# from this repo root
helm install gpu-dash ./gpu-util-dashboard \
  --create-namespace -n gpu-monitoring
```
That's it – the release pulls in Prometheus + Grafana, auto-discovers the `nvidia-dcgm-exporter` DaemonSet
and pre-loads the dashboard.

Expose Grafana:
```bash
kubectl -n gpu-monitoring port-forward svc/gpu-dash-grafana 3000:80
# → http://localhost:3000  (admin / admin)
```

---
## 3. Quick install for **no-GPU environments**
For demos the chart can self-generate metrics:
```bash
helm install gpu-dash ./gpu-util-dashboard \
  --create-namespace -n gpu-monitoring \
  --set fakeGpu.enabled=true
```
This spins up a tiny Python container that emits realistic-looking utilisation & memory numbers.

---
## 4. Customisation
* `dashboard.gpuPricePerHour` – USD cost shown in the "GPU dollars burned" panel (defaults to `3.90`).
* Everything else (Grafana admin password, retention, etc.) may be overridden with regular Helm `--set` or `-f custom.yaml`.

---
## 5. Upgrade / remove
```bash
helm upgrade  gpu-dash ./gpu-util-dashboard -n gpu-monitoring          # upgrade
helm uninstall gpu-dash                     -n gpu-monitoring          # remove everything
```

---
### File layout
```
gpu-util-dashboard/
  Chart.yaml                # declares Grafana & Prometheus as deps
  values.yaml               # sane defaults + Fake-GPU switch
  templates/
    fake-gpu-metrics.yaml   # optional synthetic metrics generator
    test-dashboard.yaml     # minimal validation board
    fixed-nvidia-dashboard.yaml  # the real DCGM board
```

---
Happy monitoring! 🚀 