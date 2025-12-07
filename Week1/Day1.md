
# Day 1 — YAML, Linux (K8s-focused), Docker, Kubernetes Basics & Release Cycle

## 1. YAML — Detailed revision notes

YAML stands for “YAML Ain’t Markup Language”. It's used widely in Kubernetes manifests, CI/CD configs, GitHub Actions, Ansible, etc.

Key rules:
- YAML depends strictly on indentation. Use spaces only — no tabs.
- Comments start with `#`.

Primary structures:
- Scalars (string, int, float, bool)
- Lists (``- item``)
- Dictionaries (`key: value`)
- Nested objects are defined by indentation

Anchors & aliases:
- `&anchor` — define a reusable block
- `*alias` — reference the anchor

Multi-line string handling:
- `|` — literal block (keeps newlines)
- `>` — folded block (removes newlines / wraps text)

Boolean values accepted: `true`, `false`, `yes`, `no`, `on`, `off`.

Common mistakes to avoid:
- Mixing tabs and spaces (will break YAML parsing)
- Incorrect nesting/indentation
- Unquoted special characters (for example `:` inside strings)

Example Kubernetes manifest snippet:

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: example
spec:
  containers:
    - name: app
      image: nginx:latest
      ports:
        - containerPort: 80
```

Note: YAML maps directly to JSON — Kubernetes ultimately reads JSON objects.

## 2. Linux notes (Kubernetes-relevant)

Although `kubectl` interacts with Kubernetes, many underlying issues require Linux debugging skills.

Useful commands:
- `ps aux` — check process states (kubelet, container runtime)
- `top` — CPU usage on nodes
- `df -h` — disk usage (common node issue)
- `free -m` — memory usage
- `systemctl status <service>` — check kubelet/container runtime service status
- `journalctl -u kubelet` — kubelet logs
- `ip a` — network interfaces
- `ip route` — routing table
- `ss -tulpn` — open/listening ports
- `curl localhost:10255` or `curl localhost:10250` — kubelet endpoints (older versions)

Kubernetes node file locations:
- `/etc/kubernetes/manifests/` — static pods (control plane components)
- `/var/lib/kubelet/` — kubelet data and configs
- `/var/log/containers/` and `/var/log/pods/` — container/pod logs

Networking / DNS tools:
- `nslookup`, `dig` — DNS debugging
- `ping`, `traceroute` — connectivity checks between nodes/pods

Linux concepts important for containers/Kubernetes:
- Namespaces — process isolation (PID, NET, MNT, IPC)
- Cgroups — resource control (CPU, memory)
- OverlayFS — layered filesystem used by container runtimes
- systemd — manages kubelet and container runtimes

## 3. Docker — detailed notes

How Docker/container runtimes work:
- Namespaces — isolate processes
- Cgroups — limit and account resources
- UnionFS/OverlayFS — layered images

Concepts:
- A Docker image is composed of read-only layers
- A container = image + read-write layer

Dockerfile basics:
- `FROM` — base image
- `COPY` / `ADD` — copy files into image
- `RUN` — build-time commands
- `CMD` — default runtime command
- `ENTRYPOINT` — fixed command executed for the container
- `EXPOSE` — documents ports
- `WORKDIR` — working directory

Layer caching:
- Each instruction creates a new image layer; changing earlier steps invalidates cache for subsequent layers.

Common Docker commands:
- Build: `docker build -t name .`
- Run: `docker run -p <host>:<container> <image>`
- List running: `docker ps`
- All containers: `docker ps -a`
- Logs: `docker logs <container-id>`
- Shell: `docker exec -it <container-id> bash`
- Cleanup: `docker system prune`

Docker vs Kubernetes runtimes:
- Kubernetes no longer depends on the Docker Engine as the CRI implementation (post v1.20+). Runtimes like `containerd` or `CRI-O` are used, but Docker images remain compatible via the OCI image spec.

Typical workflow:
1. Build Docker image
2. Push to image registry
3. Kubernetes pulls the image and runs containers

## 4. Kubernetes basics (hands-on notes)

Core objects & concepts:
- Pod — smallest deployable unit (one or more containers)
- Deployment — manages ReplicaSets and desired replica count
- ReplicaSet — maintains stable pod count
- Service — exposes pods: `ClusterIP`, `NodePort`, `LoadBalancer`
- Namespace — resource isolation
- Kubelet — node agent that communicates with the API server and manages pods on the node
- Container runtime — `containerd`, `CRI-O`
- kube-proxy — networking/routing implementation on nodes
- API Server — central entry point for cluster operations
- etcd — persistent store for cluster state

High-level request flow:
1. User runs `kubectl apply`
2. Request reaches the API server
3. API server validates and stores objects in etcd
4. Controller Manager reconciles state and creates/updates resources
5. Scheduler assigns pods to nodes
6. Kubelet pulls images and runs pods on nodes
7. kube-proxy manages pod networking/routing

Common manifest fields:
- `apiVersion`, `kind`, `metadata`, `spec`

## 5. Kubernetes release cycle notes

Release cadence & versioning:
- Kubernetes follows semantic versioning: `MAJOR.MINOR.PATCH` (for example `v1.28.3`).
- Historically minor releases were roughly quarterly; cadence and policies evolve as the project matures.

Deprecation policy & upgrade considerations:
- APIs get deprecated and are removed after a few releases — ensure manifests use stable API groups (for example `apps/v1`).
- Cloud providers and add-on components may lag behind in support for newer Kubernetes versions.

Upgrade path basics:
- Upgrade control plane components first, then worker nodes
- Verify runtime and addon compatibility (CNI, CSI)
- Confirm CRDs and custom controllers support newer versions

End of Day 1 — summary

- Revised YAML details (anchors, multiline blocks, indentation rules)
- Reviewed Linux commands and concepts relevant to Kubernetes debugging
- Refreshed Docker fundamentals and internal concepts
- Studied Kubernetes architecture, core objects, and the release lifecycle
- Solid foundational day to continue forward
