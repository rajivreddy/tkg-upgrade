# Upgrading TKG Cluster from 1.6.x to 2.3.x

### Steps
1. Download and Install the New Version of the Tanzu CLI
   1. Delete `tkg-compatibility.yaml` file
   2. Make sure you have the right versions of tanzu cli
2. Download the OVA Image templates and Import them
   1. Download all the supported `tkr` versions OVA templates.
3. Upgrade Management Cluster
   1. Update changed Avi certificate in the ako-operator-addon secret values
   2. Upgrade Management cluster
   3. List the clusters and confirm all the components in management cluster are running
   [Known Issues with AVI and others]([https://](https://docs.vmware.com/en/VMware-Tanzu-Kubernetes-Grid/2.1/tkg-deploy-mc/mgmt-release-notes.html#known-issues-upgrade))
4. Upgrade Workload Clusters
   1. List all supported tkr versions
   2. Upgrade Workload cluster
   3. Test all the components are services are working as expected.
[Please refere Here for Rubbook](./Runbook.md)
