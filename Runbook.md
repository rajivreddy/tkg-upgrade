# Runbook for upgrading Tanzu Management and Workload Cluster

- [Runbook for upgrading Tanzu Management and Workload Cluster](#runbook-for-upgrading-tanzu-management-and-workload-cluster)
  - [Install/Update Tanzu CLI](#installupdate-tanzu-cli)
    - [Install the required version of Tanzu CLI](#install-the-required-version-of-tanzu-cli)
      - [Initialise the Tanzu CLI](#initialise-the-tanzu-cli)
    - [Update the Carvel Tools](#update-the-carvel-tools)
    - [Download Supported OVA Image templates](#download-supported-ova-image-templates)
  - [Upgrade Management Cluster](#upgrade-management-cluster)
  - [Upgrade Workload Clusters](#upgrade-workload-clusters)

## Install/Update Tanzu CLI

### Install the required version of Tanzu CLI

**Step-1**

Delete the `~/.config/tanzu/tkg/compatibility/tkg-compatibility.yaml` file.

**Step-2**

1. Go to VMware Customer Connect and log in with your VMware Customer Connect credentials.
2. Visit the Tanzu Kubernetes Grid downloads page.
3. In the VMware Tanzu Kubernetes Grid row, click Go to Downloads.
4. In the Select Version drop-down, select `2.1.1`.
5. Under Product Downloads, scroll to the section labelled VMware Tanzu CLI `2.1.1`.

```
Locate VMware Tanzu CLI for Linux and click Download Now.
```

Create a folder called `tanzu` , In the `tanzu` folder, unpack the Tanzu CLI bundle file for your operating system. To unpack the bundle file, use the extraction tool of your choice. You can use the `tar` command.

```bash
tar -xvf tanzu-cli-bundle-linux-amd64.tar.gz
```

#### Initialise the Tanzu CLI

Navigate to the `cli` subfolder under the `tanzu` folder that you unpacked in the previous section.

```bash
sudo install core/v0.28.1/tanzu-core-linux_amd64 /usr/local/bin/tanzu
```

Initialise the Tanzu CLI:

```bash
tanzu init
```

Check that the correct version of the CLI is properly installed.

```bash
tanzu version
```

Uninstall the existing plugins.

```bash
tanzu plugin clean
```

Install all the plugins for this release:

```bash
tanzu plugin sync
```

Check plugin installation status and version:

```bash
tanzu plugin list
```

### Update the Carvel Tools

### Download Supported OVA Image templates

## Upgrade Management Cluster

## Upgrade Workload Clusters
