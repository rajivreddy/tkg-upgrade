# Upgrade TKG Cluster from 1.6.x to 2.3.x

- [Upgrade TKG Cluster from 1.6.x to 2.3.x](#upgrade-tkg-cluster-from-16x-to-23x)
  - [Upgrade Path](#upgrade-path)
    - [Process to Upgrade TKG Cluster](#process-to-upgrade-tkg-cluster)
    - [Update Tanzu CLI](#update-tanzu-cli)
    - [Upgrade Management Cluster](#upgrade-management-cluster)
    - [Upgrade Workload Clusters](#upgrade-workload-clusters)
    - [Cleanup Resourses](#cleanup-resourses)

## Upgrade Path

![workflow](./Images/Workflow.drawio.png)

### Process to Upgrade TKG Cluster

### Update Tanzu CLI

- [ ] Install the required version of Tanzu CLI.
  - [ ] Delete the `~/.config/tanzu/tkg/compatibility/tkg-compatibility.yaml` file.
  - [ ] Update the Carvel Tools
- [ ] Download supported OVA Image templates and Import them to Vsphere.
  - [ ] Download supported Kubernetes OVA files and import them to Vsphere.

### Upgrade Management Cluster

- [ ] Upgrade Tanzu management cluster to the latest supported version.
- [ ] Confirm the upgrade was successful.

[Known Issues](https://docs.vmware.com/en/VMware-Tanzu-Kubernetes-Grid/2.1/tkg-deploy-mc/mgmt-release-notes.html#known-issues-upgrade)

### Upgrade Workload Clusters

- [ ] List all supported tkr versions.
Upgrade the workload cluster to the latest Kubernetes version that is supported.
- [ ] Test all the components to see if the services are working as expected.

Repeat the same process for upgrading from `2.1.x` to `2.2.x` and then to `2.3.x`

### Cleanup Resourses

- [ ] Delete Any unsued OVA tempaltes
- [ ] Delete Any resoursed that are provisioned while upgrading(Nolonger needed).

[Please refer here for the runbook.](./Runbook.md)

**Reference:**

<https://docs.vmware.com/en/VMware-Tanzu-Kubernetes-Grid/2.1/tkg-deploy-mc/mgmt-upgrade-index.html>
