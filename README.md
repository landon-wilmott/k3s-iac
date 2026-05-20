# K3s Infrastructure as Code (IaC) Homelab

This repository contains the declarative configuration and YAML manifests for my [K3s](https://k3s.io/) homelab cluster. It includes all the services, deployments, and routing rules required to reproduce the environment.

## Prerequisites

Before applying the manifests in this repository, ensure that you have K3s running on at least one node. You can install K3s easily with the following command:

```bash
curl -sfL https://get.k3s.io | sh -
```

This documentation also assumes you have:
1. Local DNS Server (Pi-Hole, AdGuard, NextDNS)
2. Reverse Proxy (Traefik, Nginx)
## Procedure

Before deploying the files in this repository begin by deploying the [longhorn deployment](/namespaces/longhorn/README.md).
