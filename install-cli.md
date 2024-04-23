- [Install Tanzu CLI using Package Manager](#install-tanzu-cli-using-package-manager)
    - [For Debian/Ubuntu](#for-debianubuntu)
    - [Install CLI](#install-cli)
    - [Install the Tanzu CLI Plugins for TKG v2.3](#install-the-tanzu-cli-plugins-for-tkg-v23)
    - [Install Kubectl](#install-kubectl)

# Install Tanzu CLI using Package Manager

### For Debian/Ubuntu

```bash
sudo apt update
sudo apt install -y ca-certificates curl gpg
sudo mkdir -p /etc/apt/keyrings
curl -fsSL https://packages.vmware.com/tools/keys/VMWARE-PACKAGING-GPG-RSA-KEY.pub | sudo gpg --dearmor -o /etc/apt/keyrings/tanzu-archive-keyring.gpg
echo "deb [signed-by=/etc/apt/keyrings/tanzu-archive-keyring.gpg] https://storage.googleapis.com/tanzu-cli-os-packages/apt tanzu-cli-jessie main" | sudo tee /etc/apt/sources.list.d/tanzu.list
sudo apt update
sudo apt-cache search tanzu-cli
sudo apt list -a tanzu-cli
```

### Install CLI

```bash
sudo apt install -y tanzu-cli=$VERSION
```

### Install the Tanzu CLI Plugins for TKG v2.3

```bash
tanzu plugin group get vmware-tkg/default:v2.3.1
```

The output looks similar to the following:

```bash
Plugins in Group:  vmware-tkg/tkg:v2.3.1
NAME                TARGET      VERSION
isolated-cluster    global      v0.30.2
management-cluster  kubernetes  v0.30.2
package             kubernetes  v0.30.2
pinniped-auth       global      v0.30.2
secret              kubernetes  v0.30.2
telemetry           kubernetes  v0.30.2
```

To list all available versions for the vmware-tkg/default plugin group, run:

```bash
tanzu plugin group search -n vmware-tkg/default --show-details
```

Install the standalone plugins for Tanzu Kubernetes Grid v2.3:

```bash
tanzu plugin install --group vmware-tkg/default:v2.3.1
```

Verify that the plugins installed successfully:

```bash
tanzu plugin list
```

### Install Kubectl

[Update caravel and Kubectl](./Runbook.md#update-the-carvel-tools-and-kubectl)