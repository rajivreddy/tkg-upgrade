# Upgrading TKG Cluster from 1.6.x to 2.3.x

## Uprade Path
![workflow](./Images/Workflow.drawio.png)

### Process to Upgrade TKG Cluster
### Update Tanzu CLI
  - [ ] Install the required version of Tanzu CLI
  - [ ] Delete the `~/.config/tanzu/tkg/compatibility/tkg-compatibility.yaml` file. 
  - [ ] Update the Carvel Tools 
- [ ] Download Supported OVA Image templates and Import them to Vsphere
  - [ ] Download Supported Kubernetes OVA files and import them to Vsphere

### Upgrade Management Cluster
- [ ] Upgrade Tanzu management cluster to latest supported version
- [ ] Confirm the upgrade successful

[Known Issues with AVI and others]([https://](https://docs.vmware.com/en/VMware-Tanzu-Kubernetes-Grid/2.1/tkg-deploy-mc/mgmt-release-notes.html#known-issues-upgrade))


### Upgrade Workload Clusters
- [ ] List all supported tkr versions
- [ ] Upgrade Workload cluster to the latest kubernetes version that is supported
- [ ] Test all the components are services are working as expected.

Repeate the same process for upgrading from `2.1.x` to `2.2.x` and then to `2.3.x`

[Please refere Here for Rubbook](./Runbook.md)
