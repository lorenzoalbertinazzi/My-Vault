---
title: Kubernetes and Container Orchestration
date: 2026-05-30
tags: [kubernetes, containers, devops, cloud, orchestration, microservices, etcd, HPA, VPA, RBAC, GitOps, CNI, Borg, ArgoCD, eBPF, service-mesh, Helm, operators, declarative-config, control-plane]
source: "Verma et al. (2015) Large-scale cluster management at Google with Borg (EuroSys); Burns et al. (2016) Borg, Omega, and Kubernetes (ACM Queue); Kubernetes official documentation (CNCF, 2026); Hightower et al. 'Kubernetes: Up and Running' (O'Reilly); Beyer et al. 'Site Reliability Engineering' (Google, 2016)"
last_updated: 2026-05-31
---
## Summary
Kubernetes (K8s) is the dominant open-source container orchestration platform, managing the deployment, scaling, networking, and lifecycle of containerized applications across clusters of machines. Born from Google's internal Borg/Omega systems and open-sourced in 2014, Kubernetes solved the fundamental challenge of running applications at scale without tying them to specific infrastructure. By 2026, Kubernetes is the de facto standard for cloud-native application deployment, running on all major cloud platforms and forming the foundation of the modern DevOps and platform engineering disciplines. Understanding Kubernetes requires understanding both its declarative control-plane architecture and the distributed systems principles underpinning it.

## Key Points
- **Declarative model**: users describe desired state (in YAML); Kubernetes' control loop continuously reconciles actual state with desired state — this is the core operational philosophy
- **Pod**: the fundamental scheduling unit — one or more tightly coupled containers sharing network namespace and storage; ephemeral by design
- **Control plane vs. data plane**: the control plane (API server, etcd, scheduler, controller manager) manages cluster state; worker nodes (kubelet, container runtime, kube-proxy) run workloads
- **Service mesh**: for production microservices, service meshes (Istio, Linkerd) extend Kubernetes with mutual TLS, circuit breaking, observability — the abstraction layer above raw K8s networking
- **GitOps**: the operational model where Kubernetes cluster state is defined in Git repositories and continuously reconciled by tools like ArgoCD or Flux — the dominant deployment pattern in 2026

## Details

### Origins: From Borg to Kubernetes
Google ran **Borg** (and later Omega) — internal cluster management systems managing tens of thousands of machines and hundreds of thousands of jobs — for over a decade before Kubernetes. Key Borg paper (Verma et al., 2015) revealed:
- Borg packed jobs using bin-packing algorithms to maximize resource utilization
- Google achieved ~50% higher utilization than industry average by mixing long-running services with batch jobs
- Failure was the baseline assumption: machines crashed constantly; the system recovered automatically

**Kubernetes (2014)**: Google engineers (Joe Beda, Brendan Burns, Craig McLuckie) rewrote Borg's ideas as open-source, announced at DockerCon 2014. Donated to the Cloud Native Computing Foundation (CNCF) in 2016. Now the most active open-source project after Linux, with >3,000 contributors.

**The container prerequisite**: Docker (2013) made Linux containers (LXC, cgroups, namespaces) accessible to developers. Containers are not VMs — they share the host kernel, are lightweight (start in milliseconds), and are image-based (immutable, reproducible). The Dockerfile → image → container workflow became the standard unit of software packaging.

**Why containers over VMs:**
- VM: full OS virtualization → hundreds of MB → seconds to start → hypervisor overhead
- Container: kernel namespaces + cgroups → tens of MB → milliseconds to start → near-native performance
- Immutable images: same image runs in dev, staging, and production — eliminates "works on my machine" problem

### Architecture: Control Plane and Data Plane

**Control Plane** (master nodes):

1. **API Server (kube-apiserver)**: single point of entry for all K8s operations. RESTful API accepting JSON/YAML. All components communicate through the API server; the only component that writes to etcd. Horizontally scalable; requests authenticated, authorized (RBAC), and admission-controlled.

2. **etcd**: distributed key-value store (based on Raft consensus algorithm) storing all cluster state. ~3 or 5 replicas for high availability. etcd is the source of truth: if you lose etcd without backup, the entire cluster state is gone. Writes are linearizable (strongly consistent); reads are default linearizable but can be stale for performance.

3. **Scheduler (kube-scheduler)**: watches for unscheduled pods → selects the optimal node based on: resource requests/limits, affinity/anti-affinity rules, taints and tolerations, topology spread constraints. Two-phase: filtering (remove ineligible nodes) → scoring (rank eligible nodes). Default scoring prefers spreading replicas across nodes and failure domains.

4. **Controller Manager**: runs reconciliation loops — each controller watches a resource type and ensures actual state matches desired state:
   - ReplicaSet controller: maintains N pod replicas
   - Deployment controller: manages rolling updates
   - Node controller: evicts pods from failed nodes
   - EndpointSlice controller: maintains service-to-pod mappings

**Data Plane** (worker nodes):

1. **kubelet**: agent running on every node. Receives pod specs from API server → instructs container runtime to create/destroy containers → monitors container health → reports status back to API server. The kubelet is the lowest-level component in K8s that actually runs containers.

2. **Container Runtime**: pluggable via Container Runtime Interface (CRI). containerd (default in K8s 1.24+) and CRI-O are the main runtimes. Docker's own runtime was deprecated in 2022 (K8s no longer ships dockershim).

3. **kube-proxy**: manages network rules on each node (iptables or ipvs) to route Service traffic to the correct pod endpoints.

### Core Objects: Pods, Deployments, Services

**Pod** — the atomic unit:
```yaml
apiVersion: v1
kind: Pod
metadata:
  name: web-server
spec:
  containers:
  - name: nginx
    image: nginx:1.26
    ports:
    - containerPort: 80
    resources:
      requests:
        memory: "64Mi"
        cpu: "250m"
      limits:
        memory: "128Mi"
        cpu: "500m"
```
Pods are mortal: IPs are ephemeral, names are ephemeral. Never rely on a specific pod's IP.

**Deployment** — desired state for stateless workloads:
Manages ReplicaSets; handles rolling updates and rollbacks. Key fields: `replicas`, `strategy` (RollingUpdate with `maxSurge`/`maxUnavailable`), `selector`, `template`.

Rolling update lifecycle: K8s creates new ReplicaSet → scales up new pods → health checks pass → scales down old ReplicaSet. Zero-downtime by default if readiness probes are configured correctly.

**StatefulSet** — for stateful workloads (databases, Kafka, Elasticsearch):
Provides: stable network identity (pod-0, pod-1...), stable persistent storage (PVC bound per pod), ordered startup/shutdown. Does NOT provide data replication — the application must handle its own consistency (Kafka, Cassandra, PostgreSQL replication is application-level).

**Service** — stable network endpoint:
Abstracts over pod IP churn. Types:
- `ClusterIP`: internal VIP, cluster-only — most common for microservice-to-microservice communication
- `NodePort`: exposes on every node's IP on a fixed port (30000–32767)
- `LoadBalancer`: provisions cloud load balancer (AWS ELB, GCP GLB) — external access
- `ExternalName`: DNS CNAME alias

**Ingress** — HTTP/HTTPS routing: Routes external traffic to Services based on hostname/path. Requires an Ingress controller (NGINX, Traefik, AWS ALB Ingress Controller). Gateway API (K8s 1.28+) supersedes Ingress with richer routing semantics.

### Scheduling: Resources, Affinity, and Topology
Every container should declare resource `requests` (scheduler uses this for placement) and `limits` (runtime enforces these):
- **CPU requests**: guaranteed CPU time, in millicores (1000m = 1 CPU)
- **Memory requests**: scheduling guarantee; memory limits trigger OOMKill if exceeded
- **CPU throttling**: containers exceeding CPU limits are throttled (not killed)

**Quality of Service classes:**
- `Guaranteed`: requests == limits → scheduled on high-quality nodes, last to be evicted
- `Burstable`: requests < limits → middle priority
- `BestEffort`: no requests/limits → first to be evicted under pressure

**Pod Disruption Budgets (PDB)**: guarantee minimum availability during voluntary disruptions (node maintenance). `minAvailable: 2` ensures 2 pods always up during drain operations.

**Horizontal Pod Autoscaler (HPA)**: scales replicas based on metrics (CPU%, memory, custom metrics from Prometheus via metrics adapter). 15-second poll interval; stabilization windows prevent flapping. Target CPU utilization: typically 60–70% (not 100% — leaves headroom for sudden traffic spikes).

**Vertical Pod Autoscaler (VPA)**: adjusts resource requests/limits based on historical usage. Requires pod restarts to apply (limitation vs. HPA). Goldilocks (open-source tool) uses VPA in recommendation mode to right-size workloads.

**Cluster Autoscaler**: provisions or deprovisions EC2/GCE nodes based on pending pods and underutilization. Works with cloud provider Node Groups; critical for cost optimization (scale to zero overnight for dev clusters).

### Networking: CNI, Services, and DNS
The **Container Network Interface (CNI)** is the pluggable network layer. Popular implementations:
- **Calico**: policy-rich, BGP-based routing, high performance
- **Cilium**: eBPF-based, replaces iptables for high-throughput networking, native service mesh capabilities
- **Flannel**: simple overlay, development-friendly
- **AWS VPC CNI**: assigns VPC IPs directly to pods (native AWS routing, no overlay)

**CoreDNS** provides in-cluster DNS. Service `my-service` in namespace `my-namespace` resolves to:
`my-service.my-namespace.svc.cluster.local`

Cross-namespace communication: pods must use the FQDN; same-namespace communication can use the short name.

**Network Policies**: Kubernetes firewall rules at the pod level. Default: all pods can communicate with all pods. With a default-deny policy:
```yaml
kind: NetworkPolicy
spec:
  podSelector: {}
  policyTypes: [Ingress, Egress]
```
All ingress/egress is blocked unless explicitly allowed. Zero-trust networking requires NetworkPolicy enforcement; not all CNIs support it (Flannel does not; Calico/Cilium do).

### Storage: PV, PVC, StorageClass
**PersistentVolume (PV)**: cluster-level storage resource (AWS EBS, GCE PD, NFS, local disk)
**PersistentVolumeClaim (PVC)**: pod request for storage of a certain size/access mode
**StorageClass**: defines the provisioner and parameters; enables **dynamic provisioning** (K8s automatically creates PV when PVC is created)

Access modes:
- `ReadWriteOnce (RWO)`: single node read/write — EBS, GCE PD
- `ReadWriteMany (RWX)`: multi-node read/write — NFS, Azure File, EFS
- `ReadOnlyMany (ROX)`: multi-node read-only

StatefulSet + PVC is the standard pattern for databases. Each pod gets its own PVC via `volumeClaimTemplates`.

### Security Model: RBAC, NetworkPolicy, Pod Security
**RBAC** (Role-Based Access Control):
- `ClusterRole/Role`: defines permissions (verbs: get, list, create, delete on resources)
- `ClusterRoleBinding/RoleBinding`: grants Role to ServiceAccount/User/Group

Principle of least privilege: each microservice's ServiceAccount should only have necessary permissions. Common mistake: using the `default` ServiceAccount with admin privileges.

**Pod Security Standards** (replaced Pod Security Policy in K8s 1.25):
- `Privileged`: no restrictions (avoid in production)
- `Baseline`: prevents known privilege escalation
- `Restricted`: hardened, requires non-root, read-only root filesystem, dropped capabilities

**Secrets**: base64-encoded (NOT encrypted at rest by default). For production: enable encryption at rest OR use external secret management (Vault, AWS Secrets Manager with External Secrets Operator). Sealed Secrets (Bitnami) enables GitOps-safe secret storage.

### Production Patterns: GitOps, Service Mesh, Observability
**GitOps** (Weaveworks, 2017): cluster state defined in Git; controller continuously reconciles. ArgoCD (CNCF graduated project) watches Git → detects drift → applies changes. Benefits: audit trail, rollback via `git revert`, branch-per-environment workflows.

**Service Mesh**: Istio (Google/IBM, 2017) injects Envoy sidecar proxies into every pod, intercepting all network traffic:
- Mutual TLS (mTLS): automatic pod-to-pod encryption and authentication
- Traffic management: canary deployments, circuit breakers, retries, timeouts
- Observability: distributed tracing (Jaeger/Zipkin), metrics (Prometheus), access logs

Cilium Mesh uses eBPF instead of sidecars — lower overhead (~30% CPU reduction vs. Istio sidecars).

**The Observability Three Pillars:**
1. **Metrics**: Prometheus (pull-based, TSDB) + Grafana dashboards. `kube-state-metrics` exposes Kubernetes object state; `node-exporter` exposes node-level metrics
2. **Logs**: structured JSON logs → Fluentd/Fluent Bit → Elasticsearch/OpenSearch or Loki (label-based, lower cost)
3. **Traces**: OpenTelemetry instrumentation → Jaeger/Tempo for distributed request tracing across microservices

**SLOs and error budgets**: Kubernetes dashboards enable tracking Service Level Objectives (SLOs): "99.9% of requests complete in <200ms." Error budget = 1 − SLO = tolerated downtime/errors. Exceeding error budget pauses feature development until reliability is restored.

### Kubernetes at Scale: Multi-Cluster and Federation
Single Kubernetes cluster limits: ~5,000 nodes, ~150,000 pods, ~300,000 containers (K8s 1.28+). Beyond this, multi-cluster strategies are required.

**Multi-cluster patterns:**
- **Cluster per team** (cell-based): each team owns a cluster; central platform team provides base configuration via cluster templates
- **Hub-and-spoke**: control plane cluster manages workload clusters (ArgoCD, Crossplane)
- **Cluster federation**: Karmada and Liqo provide true cross-cluster scheduling

Large-scale K8s users: Google (millions of pods), Spotify (~20,000 microservices across K8s clusters), Airbnb, Pinterest, Zalando (pioneered open-source K8s tooling).

## Related
- [[docker-and-containerization]] — Containers are the foundation of Kubernetes; Docker image building and container runtime concepts
- [[agentic-ai-and-multi-agent-systems]] — Kubernetes is the infrastructure layer for deploying AI agent clusters and model serving
- [[machine-learning-fundamentals]] — MLOps and model serving rely on Kubernetes for scalable ML inference
- [[retrieval-augmented-generation]] — RAG pipelines and vector database deployments are orchestrated on Kubernetes
- [[transformer-architecture]] — Large language model serving (vLLM, TGI, Triton) requires Kubernetes GPU scheduling
- [[llm-training-and-scaling-laws]] — Distributed training frameworks (Kubeflow, Ray) run on Kubernetes
- [[cryptography-fundamentals-and-zero-knowledge-proofs]] — TLS certificate management (cert-manager) and secret handling are core Kubernetes security concerns
