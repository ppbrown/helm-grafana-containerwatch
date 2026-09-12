# helm-grafana-containerwatch

Pre-configured cluster-wide container monitoring dashboard.

Alloy (DaemonSet, built-in cAdvisor module) + Prometheus + Grafana, packaged
as a Helm chart. Alloy collects per-container metrics on every node and
pushes them to Prometheus via `remote_write`; Grafana comes pre-provisioned
with a Prometheus datasource and a "Container Statuses" dashboard.


## Values

| Key | Default | Notes |
|---|---|---|
| `prometheus.image.tag` | `v3.14.0` | |
| `prometheus.service.type` | `ClusterIP` | `ClusterIP` / `NodePort` / `LoadBalancer` |
| `alloy.image.tag` | `v1.19.2` | |
| `alloy.containerdHostDir` / `alloy.containerdSocketPath` | `/run/containerd`, `/run/containerd/containerd.sock` | Set to k3s's paths (`/run/k3s/containerd/...`) on k3s clusters |
| `alloy.dockerSocketPath` | `/var/run/docker.sock` | |
| `grafana.image.tag` | `13.2.1` | |
| `grafana.adminUser` / `grafana.adminPassword` | `admin` / `admin` | Set via env vars, no forced first-login change |
| `grafana.service.type` | `ClusterIP` | `ClusterIP` / `NodePort` / `LoadBalancer` |
| `grafana.ingress.enabled` / `.className` / `.host` / `.annotations` / `.tls` | `false` / `""` / `""` / `{}` / `[]` | |
| `grafana.dashboards.enabled` | `true` | Provisions the "Container Statuses" dashboard |

Prometheus and Grafana use `emptyDir` storage - data is lost on pod
restart/reschedule.

## Access

Login with `grafana.adminUser`/`grafana.adminPassword`.

**Port-forward** (default, no config needed):

    kubectl port-forward -n <namespace> svc/grafana 3000:3000

**LoadBalancer** - set `grafana.service.type=LoadBalancer`, then get the external IP:

    kubectl get svc -n <namespace> grafana

**Ingress** - set `grafana.ingress.enabled=true` plus `.className`/`.host`/`.tls` as needed.

