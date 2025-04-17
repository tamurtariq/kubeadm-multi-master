# Kubernetes Multi-Master Setup Script (with HAProxy Load Balancer)

This script automates the installation and configuration of a Kubernetes control plane node with a multi-master setup behind a load balancer (e.g., HAProxy or Keepalived). It configures container runtime, installs Kubernetes components, sets up networking, and generates the join command for adding additional master nodes.


## 🧰 Prerequisites

- Ubuntu 22.04/24.04 based system (for each master node)
- Static IP addresses assigned to each master
- A load balancer (e.g., HAProxy or Keepalived) with a Virtual IP configured to distribute traffic to all master nodes on port `6443`
- Internet access from each node
- Script should be run as a user with `sudo` privileges

---

## 📦 What This Script Does

1. Cleans up any existing Kubernetes setup
2. Disables swap (required by Kubernetes)
3. Configures `containerd` as the container runtime
4. Installs required kernel modules and sysctl settings
5. Installs Docker (as an additional runtime if needed)
6. Installs Kubernetes components: `kubelet`, `kubeadm`, and `kubectl`
7. Initializes the Kubernetes control plane using the HAProxy virtual IP
8. Applies Flannel as the CNI plugin
9. Generates a join command for additional master nodes
10. Verifies node status after initialization

---

## ⚙️ Configuration

Before running the script, make sure to update the following variable inside the script:

```bash
LOAD_BALANCER_IP="192.168.1.100"  # <-- Change this to your HAProxy/Keepalived VIP
```

---

## 🚀 Usage

1. Copy the script to your primary master node:

   ```bash
   nano k8s-master-setup.sh
   ```

2. Make it executable:

   ```bash
   chmod +x k8s-master-setup.sh
   ```

3. Run the script:

   ```bash
   ./k8s-master-setup.sh
   ```

---

## 📜 Output

- On successful completion:
  - Kubernetes will be initialized with the specified control-plane endpoint.
  - Flannel will be applied as the networking plugin.
  - A join command for additional masters will be saved to `~/join-command.txt`.

Example:

```
To join additional masters, run the following command on each master node:

kubeadm join 192.168.1.100:6443 --token <your-token> \
    --discovery-token-ca-cert-hash sha256:<hash> \
    --control-plane --certificate-key <cert-key>
```

---

## 🧹 Cleanup (Optional)

If needed, reset your Kubernetes setup:

```bash
sudo kubeadm reset -f
sudo rm -rf /etc/kubernetes/ /var/lib/etcd ~/.kube
```

---

## 📘 References

- [Kubernetes Setup Docs](https://kubernetes.io/docs/setup/)
- [Flannel CNI](https://github.com/flannel-io/flannel)
- [HAProxy](https://www.haproxy.org/)
- [Keepalived](https://www.keepalived.org/)

---

## ❗ Troubleshooting

- If `kubeadm init` fails, check system logs:
  ```bash
  journalctl -xeu kubelet
  ```
- Make sure the control-plane endpoint (load balancer IP) is reachable from all nodes.
- Ensure that ports `6443`, `2379-2380`, `10250`, and `10257` are open between nodes.

---

## 🛡️ License

This script is provided as-is under the MIT License.
```

---

Let me know if you want this README saved to a file or customized for worker nodes or HAProxy setup too!
