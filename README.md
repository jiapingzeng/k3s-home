# k3s-home

Home automation setup with Home Assistant, Pi-hole, and Traefik on k3s.

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

## Architecture

- **Home Assistant**: Home automation platform with web UI
- **Pi-hole**: DNS ad-blocker and DHCP server
- **Traefik**: Reverse proxy exposing Home Assistant on port 8123

## Structure

```
├── kustomization.yaml          # Root deployment config
├── config.properties.template # Configuration template
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
└── pihole/                   # Pi-hole DNS ad-blocker
    ├── kustomization.yaml     # Service deployment
    └── resources/            # Kubernetes resources
        ├── namespace.yaml
        ├── deployment.yaml    # Main application with init container
        ├── persistentvolume.yaml # Data storage (2GB)
        ├── persistentvolumeclaim.yaml
        └── service.yaml       # LoadBalancer service (DNS + Web)
```


## Requirements

- k3s cluster
- 12GB+ available storage (Home Assistant: 10GB, Pi-hole: 2GB)
- Network access to ports: 8053 (DNS), 8080 (Pi-hole web), 8123 (Home Assistant)

## Customization

To customize data path or timezone:

```bash
# Copy template and edit values
cp config.properties.template config.properties
vi config.properties
```

## Cleanup

```bash
# Clean removal of everything deployed
kubectl delete -k .
```