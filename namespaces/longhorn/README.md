# Longhorn Storage Deployment

This directory contains the Infrastructure as Code (IaC) definitions for deploying [Longhorn](https://longhorn.io/), a highly available, distributed block storage solution for Kubernetes.

## Prerequisites

Before applying these manifests, ensure that your K3s environment meets the following requirements:

1. **K3s Cluster**: A running K3s cluster.
2. **Ingress Controller**: Traefik (default with K3s) or your preferred ingress controller must be running to route traffic to the Longhorn UI.
3. **Node Dependencies**: Every worker node in your K3s cluster **must** have the following packages installed. Without these, Longhorn will fail to attach volumes to your pods.
   * `open-iscsi`
   * `nfs-common` (or `nfs-utils` depending on your OS)
   * `util-linux`
   * `bash`

   On Debian/Ubuntu-based nodes, you can install the dependencies using:
   ```bash
   sudo apt-get update
   sudo apt-get install open-iscsi nfs-common util-linux bash
   ```

## Deployment

The configuration in this directory is declarative and contains the necessary Kubernetes resources to get Longhorn running.

Before deploying the file ensure that you:
1. Change [CHOSEN_URL] in the Ingress resource to your desired URL. 
2. Create longhorn-system namespace by running command:

```bash
kubectl create namespace longhorn-system
```

To deploy or update Longhorn, apply the YAML manifests located in this directory:

```bash
kubectl apply -f .
```

Verify that the deployment was successful and all Longhorn pods are running properly:

```bash
kubectl get pods -n longhorn-system --watch
```

## Accessing the Dashboard

An Ingress resource is defined in the YAML configuration to expose the Longhorn UI. 

1. Check the Ingress file in this directory to find the configured hostname (e.g., `longhorn.yourdomain.com`).
2. Ensure your DNS provider or local `/etc/hosts` file resolves that hostname to your K3s cluster's Ingress IP.
3. Navigate to the hostname in your web browser.

## Security Note

By default, the Longhorn UI does not have authentication enabled. Ensure that the Ingress is protected using Basic Authentication, OAuth, or an external authentication provider (such as Authelia or Keycloak) before exposing it publicly. 

## Day 2 Operations

Once Longhorn is successfully deployed, you may want to perform additional cluster configurations to tailor it to your needs.

### Adding Dedicated Disks to Nodes

To expand your cluster's storage capacity, you can attach additional drives to your Longhorn nodes. 

*Note: Ensure the new drive is partitioned, formatted (e.g., `ext4` or `xfs`), and mounted to a directory on the worker node (e.g., `/mnt/storage`) before proceeding.*

1. Access the Longhorn dashboard via your configured Ingress.
2. Navigate to the **Nodes** tab in the top navigation bar.
3. Locate the node where you attached the new drive.
4. Click the **hamburger menu** (⋮) under the **Operation** column and select **Edit Node and Disks**.
5. Click **Add Disk** and provide the necessary configuration:
   * **Path**: The mount path on the host (e.g., `/mnt/storage`).
   * **Scheduling**: `Enabled` (Allows Longhorn to schedule replicas on this disk).
   * **Eviction Requested**: `False` (Ensures data is not evicted from this disk).
6. Click **Save**. Longhorn will automatically detect the disk's capacity and begin using it for volume replicas.

## Teardown / Uninstallation

To remove Longhorn and its associated resources from your cluster, you can delete the manifests applied from this directory:

```bash
kubectl delete -f .
```
*Warning: This will destroy all Longhorn components. Ensure you have backed up any critical volume data before proceeding, as deleting the namespace/system may result in data loss depending on your reclaim policies.*

## References

* [Longhorn Official Documentation](https://longhorn.io/docs/1.11.2/)
* [K3s Official Documentation](https://docs.k3s.io/quick-start)