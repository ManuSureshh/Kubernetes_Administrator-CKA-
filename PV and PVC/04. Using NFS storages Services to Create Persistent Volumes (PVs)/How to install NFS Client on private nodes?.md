## How to install NFS Client if the K8s Nodes has no public IP?
1. **SSH Access via a Bastion Host**
- If your node does not have a public IP but is in a private network, you might use a bastion host (jump box) to access it.
  - Connect to the Bastion Host:
    ```
    ssh user@bastion-host-ip
    ```
  - SSH from Bastion Host to Target Node:
    ```
    ssh user@private-node-ip
    ```
  - Install nfs-common:
    ```
    sudo apt-get update
    ```
    ```
    sudo apt-get install nfs-common
    ```

2. **Kubernetes DaemonSet**
- You can use a Kubernetes DaemonSet to run a job on all nodes in the cluster.
- This method is useful if you need to perform operations on all nodes without direct SSH access.
  - Create a DaemonSet:
    ```
    apiVersion: apps/v1
    kind: DaemonSet
    metadata:
      name: install-nfs-common
      namespace: kube-system
    spec:
      selector:
        matchLabels:
          name: install-nfs-common
      template:
        metadata:
          labels:
            name: install-nfs-common
        spec:
          containers:
          - name: install-nfs-common
            image: ubuntu:latest
            command: ["/bin/sh"]
            args: ["-c", "apt-get update && apt-get install -y nfs-common"]
            securityContext:
              privileged: true
```

4.  Using VPN