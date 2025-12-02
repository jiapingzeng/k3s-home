# k3s-home

Home server setup with Home Assistant, Pi-hole, and Ollama on k3s.

## Quick Start

```bash
# Create data directory
sudo mkdir -p /mnt/k3s-home

# Deploy everything
kubectl apply -k .
```

Access services at:
- **Home Assistant**: `http://SERVER_IP:8123`
- **Pi-Hole**: `http://SERVER_IP:8080` (default password: `admin`)
- **Ollama**: `http://SERVER_IP:11434` (API endpoint)

## Architecture

- **Home Assistant**: Home automation platform with web UI
- **Pi-hole**: DNS ad-blocker and DHCP server
- **Ollama**: Local LLM inference server for AI models
- **Traefik**: Reverse proxy exposing Home Assistant on port 8123

## Structure

```
├── kustomization.yaml          # Root deployment config
├── config.properties.template # Configuration template
├── gpu-support/               # NVIDIA GPU support
│   ├── kustomization.yaml     # GPU device plugin deployment
│   └── resources/            # GPU-related resources
│       └── nvidia-device-plugin.yaml # NVIDIA device plugin DaemonSet
├── home-assistant/            # Home Assistant service
│   ├── kustomization.yaml     # Service deployment
│   └── resources/            # Kubernetes resources
│       ├── namespace.yaml
│       ├── configmap.yaml     # Config with reverse proxy trust
│       ├── deployment.yaml    # Main application with init container
│       ├── ingress.yaml       # Traefik routing
│       ├── persistentvolume.yaml # Data storage (10GB)
│       ├── persistentvolumeclaim.yaml
│       └── service.yaml       # Internal service
├── traefik/                  # Traefik reverse proxy
│   ├── kustomization.yaml     # Service deployment
│   └── resources/            # Kubernetes resources
│       ├── namespace.yaml
│       ├── serviceaccount.yaml
│       ├── rbac.yaml         # Cluster permissions
│       ├── configmap.yaml    # Traefik config (port 8123)
│       ├── deployment.yaml   # Proxy application
│       ├── service.yaml      # LoadBalancer service
│       └── ingressclass.yaml # Default ingress class
├── pihole/                   # Pi-hole DNS ad-blocker
│   ├── kustomization.yaml     # Service deployment
│   └── resources/            # Kubernetes resources
│       ├── namespace.yaml
│       ├── deployment.yaml    # Main application with init container
│       ├── persistentvolume.yaml # Data storage (2GB)
│       ├── persistentvolumeclaim.yaml
│       └── service.yaml       # LoadBalancer service (DNS + Web)
└── ollama/                   # Ollama LLM inference server
    ├── kustomization.yaml     # Service deployment
    └── resources/            # Kubernetes resources
        ├── namespace.yaml
        ├── deployment.yaml    # Main application with GPU support
        ├── persistentvolume.yaml # Data storage (50GB)
        ├── persistentvolumeclaim.yaml
        └── service.yaml       # LoadBalancer service (API)
```


## Requirements

- k3s cluster
- Network access to ports: 8053 (DNS), 8080 (Pi-hole web), 8123 (Home Assistant), 11434 (Ollama API)
- NVIDIA GPU with NVIDIA Container Toolkit

## Customization

To customize data path or timezone:

```bash
# Copy template and edit values
cp config.properties.template config.properties
vi config.properties
```

## GPU Setup

For Ollama GPU acceleration, see [NVIDIA Container Runtime guide](https://docs.k3s.io/advanced#nvidia-container-runtime).


## Cleanup

```bash
# Clean removal of everything deployed
kubectl delete -k .
```