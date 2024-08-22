- [Install CLI](#install-cli)
- [Pre Validation](#pre-validation)
- [Upgrade Management Cluster](#upgrade-management-cluster)
- [Upgrade Workload Clusters](#upgrade-workload-clusters)
  - [Pre Validation](#pre-validation-1)

### Install CLI
Check the old Version

```bash
tanzu version
version: v1.0.0
buildDate: 2023-08-08
sha: 006d0429
```

Download the CLI from below link

<https://github.com/vmware-tanzu/tanzu-cli/releases>

After Download move the .gzfile to a folder

```bash
mv tanzu-cli-linux-amd64.tar.gz 2.4.1/
cd 2.4.1/
```

unarchive the file

```bash
gunzip tanzu-cli-linux-amd64.tar.gz
tar -xvf tanzu-cli-linux-amd64.tar
v1.1.0/
v1.1.0/tanzu-cli-linux_amd64
```

Go to `v1.1.0/`

```bash
cd v1.1.0/
ls
tanzu-cli-linux_amd64
```

Install CLI

```bash
install tanzu-cli-linux_amd64 /usr/local/bin/tanzu
```

Check updated Version

```bash
tanzu version
version: v1.1.0
buildDate: 2023-11-01
sha: d0679f5a
arch: amd64
```

Login As normal User `vmadmin`

Delete `tkg-compatibility.yaml` file

```bash
rm -rf ~/.config/tanzu/tkg/compatibility/tkg-compatibility.yaml
```

List the standalone Tanzu CLI plugins for Tanzu Kubernetes Grid v2.4

```bash
tanzu plugin group get vmware-tkg/default:v2.4.1
[i] Reading plugin inventory for "projects.registry.vmware.com/tanzu_cli/plugins/plugin-inventory:latest", this will take a few seconds.
Plugins in Group:  vmware-tkg/default:v2.4.1
  NAME                TARGET      VERSION
  isolated-cluster    global      v0.31.1
  management-cluster  kubernetes  v0.31.1
  package             kubernetes  v0.31.1
  pinniped-auth       global      v0.31.1
  secret              kubernetes  v0.31.1
  telemetry           kubernetes  v0.31.1
```

To list all available versions for the vmware-tkg/default plugin group, run

```bash
tanzu plugin group search -n vmware-tkg/default --show-details
name: vmware-tkg/default
description: Plugins for TKG
latest: v2.5.1
versions:
    - v1.6.0
    - v1.6.1
    - v2.1.0
    - v2.1.1
    - v2.2.0
    - v2.3.0
    - v2.3.1
    - v2.4.0
    - v2.4.1
    - v2.5.0
    - v2.5.1
```

Install the standalone plugins for Tanzu Kubernetes Grid v2.4.1:

```bash
tanzu plugin install --group vmware-tkg/default:v2.4.1
[i] The following plugins will be installed from plugin group 'vmware-tkg/default:v2.4.1'
  NAME                TARGET      VERSION
  isolated-cluster    global      v0.31.1
  management-cluster  kubernetes  v0.31.1
  package             kubernetes  v0.31.1
  pinniped-auth       global      v0.31.1
  secret              kubernetes  v0.31.1
  telemetry           kubernetes  v0.31.1
[i] Installing plugin 'isolated-cluster:v0.31.1' with target 'global'
[i] Installing plugin 'management-cluster:v0.31.1' with target 'kubernetes'
[i] Installing plugin 'package:v0.31.1' with target 'kubernetes'
[i] Installing plugin 'pinniped-auth:v0.31.1' with target 'global'
[i] Installing plugin 'secret:v0.31.1' with target 'kubernetes'
[i] Installing plugin 'telemetry:v0.31.1' with target 'kubernetes'
[ok] successfully installed all plugins from group 'vmware-tkg/default:v2.4.1'
```

Verify that the plugins installed successfully:

```bash
tanzu plugin list
Standalone Plugins
  NAME                DESCRIPTION                                                        TARGET      VERSION  STATUS
  login               Login to the platform                                                          v0.29.0  installed
  isolated-cluster    Prepopulating images/bundle for internet-restricted environments   global      v0.31.1  installed
  pinniped-auth       Pinniped authentication operations (usually not directly invoked)  global      v0.31.1  installed
  telemetry           configure cluster-wide settings for vmware tanzu telemetry         global      v1.1.0   installed
  management-cluster  Kubernetes management cluster operations                           kubernetes  v0.31.1  installed
  package             Tanzu package management                                           kubernetes  v0.31.1  installed
  secret              Tanzu secret management                                            kubernetes  v0.31.1  installed
  telemetry           configure cluster-wide settings for vmware tanzu telemetry         kubernetes  v0.31.1  installed

```

List all the contexts available

```bash
[vmadmin@relprdvjhorb01 ~]$ tanzu context list
  NAME                           ISACTIVE  TYPE        ENDPOINT  KUBECONFIGPATH                  KUBECONTEXT
  nprod-tkg-management-cluster   false     kubernetes            /home/vmadmin/.kube-tkg/config  nprod-tkg-management-cluster-admin@nprod-tkg-management-cluster
  prod-tkg-management-cluster    true      kubernetes            /home/vmadmin/.kube-tkg/config  prod-tkg-management-cluster-admin@prod-tkg-management-cluster
  mirror-tkg-management-cluster  false     kubernetes            /home/vmadmin/.kube-tkg/config  mirror-tkg-management-cluster-admin@mirror-tkg-management-cluster
```

Set to right context based on what Managemet cluster you are working on

```bash
[vmadmin@relprdvjhorb01 ~]$ tanzu context use nprod-tkg-management-cluster
[i] Successfully activated context 'nprod-tkg-management-cluster'
[i] Checking for required plugins for context 'nprod-tkg-management-cluster'...
[i] All required plugins are already installed and up-to-date
```

List the plugins

```bash
tanzu plugin list
Standalone Plugins
  NAME                DESCRIPTION                                                        TARGET      VERSION  STATUS
  isolated-cluster    Prepopulating images/bundle for internet-restricted environments   global      v0.31.1  installed
  pinniped-auth       Pinniped authentication operations (usually not directly invoked)  global      v0.31.1  installed
  telemetry           configure cluster-wide settings for vmware tanzu telemetry         global      v1.1.0   installed
  diagnostics         crashd plugin                                                      kubernetes  v0.0.1   installed
  management-cluster  Kubernetes management cluster operations                           kubernetes  v0.31.1  installed
  package             Tanzu package management                                           kubernetes  v0.31.1  installed
  secret              Tanzu secret management                                            kubernetes  v0.31.1  installed
  telemetry           configure cluster-wide settings for vmware tanzu telemetry         kubernetes  v0.31.1  installed

Plugins from Context:  nprod-tkg-management-cluster
  NAME                DESCRIPTION                           TARGET      VERSION  STATUS
  cluster             Kubernetes cluster operations         kubernetes  v0.30.2  installed
  feature             Operate on features and featuregates  kubernetes  v0.30.2  installed
  kubernetes-release  Kubernetes release operations         kubernetes  v0.30.2  installed
```

Make sure your kubernetes context set to management cluster

```bash
  contexts
CURRENT   NAME                                                                  CLUSTER                         AUTHINFO                                 NAMESPACE
          mirror-tkg-management-cluster-admin@mirror-tkg-management-cluster     mirror-tkg-management-cluster   mirror-tkg-management-cluster-admin
          nprod-tkg-management-cluster-admin@nprod-tkg-management-cluster       nprod-tkg-management-cluster    nprod-tkg-management-cluster-admin
          prod-tkg-management-cluster-admin@prod-tkg-management-cluster         prod-tkg-management-cluster     prod-tkg-management-cluster-admin
          tanzu-cli-nprod-tkg-management-cluster@nprod-tkg-management-cluster   nprod-tkg-management-cluster    tanzu-cli-nprod-tkg-management-cluster
          tkg-dev-cluster-admin@tkg-dev-cluster                                 tkg-dev-cluster                 tkg-dev-cluster-admin
          tkg-mirror-cluster-admin@tkg-mirror-cluster                           tkg-mirror-cluster              tkg-mirror-cluster-admin
          tkg-preprod-cluster-admin@tkg-preprod-cluster                         tkg-preprod-cluster             tkg-preprod-cluster-admin
*         tkg-prod-cluster-admin@tkg-prod-cluster                               tkg-prod-cluster                tkg-prod-cluster-admin
          tkg-shared-service-cluster-admin@tkg-shared-service-cluster           tkg-shared-service-cluster      tkg-shared-service-cluster-admin
          tkg-staging-cluster-admin@tkg-staging-cluster                         tkg-staging-cluster             tkg-staging-cluster-admin
          tkg-test-cluster-admin@tkg-test-cluster                               tkg-test-cluster                tkg-test-cluster-admin
          tkg-uat-cluster-admin@tkg-uat-cluster                                 tkg-uat-cluster                 tkg-uat-cluster-admin
 kcu nprod-tkg-management-cluster-admin@nprod-tkg-management-cluster
Switched to context "nprod-tkg-management-cluster-admin@nprod-tkg-management-cluster".
```

List the workload clusters in a management cluster

```bash
kg clusters -A
NAMESPACE    NAME                           PHASE         AGE      VERSION
default      tkg-dev-cluster                Provisioned   2y274d
default      tkg-preprod-cluster            Provisioned   2y274d
default      tkg-staging-cluster            Provisioned   2y274d
default      tkg-test-cluster               Provisioned   2y274d
tkg-system   nprod-tkg-management-cluster   Provisioned   2y274d
```

### Pre Validation

```bash
kg nodes
NAME                                                       STATUS   ROLES           AGE    VERSION
nprod-tkg-management-cluster-control-plane-6ccgd           Ready    control-plane   138d   v1.26.8+vmware.1
nprod-tkg-management-cluster-control-plane-hbf4t           Ready    control-plane   138d   v1.26.8+vmware.1
nprod-tkg-management-cluster-control-plane-zdwzr           Ready    control-plane   138d   v1.26.8+vmware.1
nprod-tkg-management-cluster-md-0-575c696497x4c869-zfj2g   Ready    <none>          138d   v1.26.8+vmware.1
```

```bash
allpods
NAMESPACE                           NAME                                                                       READY   STATUS      RESTARTS         AGE
avi-system                          ako-0                                                                      1/1     Running     2 (71d ago)      109d
capi-kubeadm-bootstrap-system       capi-kubeadm-bootstrap-controller-manager-58f695646-xmm84                  1/1     Running     1 (90d ago)      137d
capi-kubeadm-control-plane-system   capi-kubeadm-control-plane-controller-manager-5f6c55f8d9-mdz5h             1/1     Running     0                137d
capi-system                         capi-controller-manager-ccc57b89f-b4dq4                                    1/1     Running     1 (90d ago)      137d
capv-system                         capv-controller-manager-68889d4cf-w9kth                                    1/1     Running     1 (90d ago)      137d
cert-manager                        cert-manager-5cb49bb9dc-gs42w                                              1/1     Running     0                138d
cert-manager                        cert-manager-cainjector-57f8df97f8-ztblc                                   1/1     Running     0                138d
cert-manager                        cert-manager-webhook-69656b997d-vm477                                      1/1     Running     0                138d
default                             nginx-748c667d99-zmcl5                                                     1/1     Running     0                137d
kube-system                         antrea-agent-bmpv9                                                         2/2     Running     0                138d
kube-system                         antrea-agent-dscfx                                                         2/2     Running     0                138d
kube-system                         antrea-agent-pd8lx                                                         2/2     Running     0                138d
kube-system                         antrea-agent-rbkjl                                                         2/2     Running     1 (138d ago)     138d
kube-system                         antrea-controller-5d4997d7b5-ljb5g                                         1/1     Running     0                138d
kube-system                         coredns-868fb44cc5-fnvcg                                                   1/1     Running     0                138d
kube-system                         coredns-868fb44cc5-sck4s                                                   1/1     Running     0                138d
kube-system                         etcd-nprod-tkg-management-cluster-control-plane-6ccgd                      1/1     Running     0                138d
kube-system                         etcd-nprod-tkg-management-cluster-control-plane-hbf4t                      1/1     Running     0                138d
kube-system                         etcd-nprod-tkg-management-cluster-control-plane-zdwzr                      1/1     Running     0                138d
kube-system                         kube-apiserver-nprod-tkg-management-cluster-control-plane-6ccgd            1/1     Running     0                138d
kube-system                         kube-apiserver-nprod-tkg-management-cluster-control-plane-hbf4t            1/1     Running     0                138d
kube-system                         kube-apiserver-nprod-tkg-management-cluster-control-plane-zdwzr            1/1     Running     0                138d
kube-system                         kube-controller-manager-nprod-tkg-management-cluster-control-plane-6ccgd   1/1     Running     1 (90d ago)      138d
kube-system                         kube-controller-manager-nprod-tkg-management-cluster-control-plane-hbf4t   1/1     Running     0                138d
kube-system                         kube-controller-manager-nprod-tkg-management-cluster-control-plane-zdwzr   1/1     Running     0                138d
kube-system                         kube-proxy-78s9z                                                           1/1     Running     0                138d
kube-system                         kube-proxy-q77nk                                                           1/1     Running     0                138d
kube-system                         kube-proxy-v82tg                                                           1/1     Running     0                138d
kube-system                         kube-proxy-vkpnd                                                           1/1     Running     0                138d
kube-system                         kube-scheduler-nprod-tkg-management-cluster-control-plane-6ccgd            1/1     Running     0                138d
kube-system                         kube-scheduler-nprod-tkg-management-cluster-control-plane-hbf4t            1/1     Running     0                138d
kube-system                         kube-scheduler-nprod-tkg-management-cluster-control-plane-zdwzr            1/1     Running     1 (90d ago)      138d
kube-system                         kube-vip-nprod-tkg-management-cluster-control-plane-6ccgd                  1/1     Running     0                138d
kube-system                         kube-vip-nprod-tkg-management-cluster-control-plane-hbf4t                  1/1     Running     0                138d
kube-system                         kube-vip-nprod-tkg-management-cluster-control-plane-zdwzr                  1/1     Running     0                138d
kube-system                         metrics-server-648fbdd4b9-46vll                                            1/1     Running     0                138d
kube-system                         vsphere-cloud-controller-manager-8wqvb                                     1/1     Running     1 (138d ago)     138d
kube-system                         vsphere-cloud-controller-manager-9vxgl                                     1/1     Running     1 (90d ago)      138d
kube-system                         vsphere-cloud-controller-manager-hkghh                                     1/1     Running     1 (138d ago)     138d
pinniped-concierge                  pinniped-concierge-7d8f444d48-lwp6d                                        1/1     Running     0                134d
pinniped-concierge                  pinniped-concierge-7d8f444d48-q4cqp                                        1/1     Running     0                134d
pinniped-concierge                  pinniped-concierge-kube-cert-agent-7545bc5f57-mrr9n                        1/1     Running     0                134d
pinniped-supervisor                 pinniped-post-deploy-controller-5c997cdc5b-99f5b                           1/1     Running     0                134d
pinniped-supervisor                 pinniped-post-deploy-job-mcpxf                                             0/1     Completed   0                134d
pinniped-supervisor                 pinniped-supervisor-654759c77-cnhx7                                        1/1     Running     3592 (40m ago)   134d
pinniped-supervisor                 pinniped-supervisor-654759c77-kx5jg                                        1/1     Running     3594 (25m ago)   134d
tanzu-auth                          tanzu-auth-controller-manager-677d79d844-h8dfc                             1/1     Running     0                137d
tanzu-system-logging                fluent-bit-26fwm                                                           1/1     Running     0                138d
tanzu-system-logging                fluent-bit-v8gql                                                           1/1     Running     0                138d
tanzu-system-logging                fluent-bit-xwjqs                                                           1/1     Running     0                138d
tanzu-system-logging                fluent-bit-zgvk7                                                           1/1     Running     0                138d
tkg-system-networking               ako-operator-controller-manager-d44cf6958-rf2wz                            1/1     Running     0                138d
tkg-system                          kapp-controller-6955558489-5r52g                                           2/2     Running     0                138d
tkg-system                          object-propagation-controller-manager-57d8d97c47-jqhpm                     1/1     Running     0                138d
tkg-system                          tanzu-addons-controller-manager-59468f79d9-brcwr                           1/1     Running     0                138d
tkg-system                          tanzu-capabilities-controller-manager-7c5c75c565-qgr5g                     1/1     Running     0                138d
tkg-system                          tanzu-featuregates-controller-manager-f7d4b46d-tshd9                       1/1     Running     0                138d
tkg-system                          tkr-conversion-webhook-manager-c6b69b9d4-zpcwg                             1/1     Running     0                138d
tkg-system                          tkr-resolver-cluster-webhook-manager-6456956d4c-c4f58                      1/1     Running     0                138d
tkg-system                          tkr-source-controller-manager-7b94599774-jbv98                             1/1     Running     1 (75d ago)      138d
tkg-system                          tkr-status-controller-manager-c88cd6f8-7znpc                               1/1     Running     0                138d
tkg-system                          tkr-vsphere-resolver-webhook-manager-5c79d4c6f8-hx9tg                      1/1     Running     0                138d
vmware-system-csi                   vsphere-csi-controller-7b6f5956b6-2g556                                    7/7     Running     38 (13d ago)     138d
vmware-system-csi                   vsphere-csi-controller-7b6f5956b6-7t2zt                                    7/7     Running     31 (13d ago)     138d
vmware-system-csi                   vsphere-csi-controller-7b6f5956b6-cgppb                                    7/7     Running     44 (13d ago)     138d
vmware-system-csi                   vsphere-csi-node-48qqr                                                     3/3     Running     4 (138d ago)     138d
vmware-system-csi                   vsphere-csi-node-cwvnb                                                     3/3     Running     0                138d
vmware-system-csi                   vsphere-csi-node-g22cs                                                     3/3     Running     0                138d
vmware-system-csi                   vsphere-csi-node-jrmrg                                                     3/3     Running     0                138d
```

List Tanzu workload clusters adn their TKR versions

```bash
tanzu cluster list
Downloading the TKG Bill of Materials (BOM) file from 'projects.registry.vmware.com/tkg/tkg-bom:v2.3.1'
Downloading the TKr Bill of Materials (BOM) file from 'projects.registry.vmware.com/tkg/tkr-bom:v1.26.8_vmware.1-tkg.2'
the old providers folder /home/vmadmin/.config/tanzu/tkg/providers is backed up to /home/vmadmin/.config/tanzu/tkg/providers-20240820195714-fmcde9me
  NAME                 NAMESPACE  STATUS   CONTROLPLANE  WORKERS  KUBERNETES        ROLES   PLAN  TKR
  tkg-dev-cluster      default    running  1/1           4/4      v1.26.8+vmware.1  <none>  dev   v1.26.8---vmware.1-tkg.2
  tkg-preprod-cluster  default    running  3/3           3/3      v1.26.8+vmware.1  <none>  prod  v1.26.8---vmware.1-tkg.2
  tkg-staging-cluster  default    running  1/1           2/2      v1.26.8+vmware.1  <none>  dev   v1.26.8---vmware.1-tkg.2
  tkg-test-cluster     default    running  1/1           2/2      v1.26.8+vmware.1  <none>  dev   v1.26.8---vmware.1-tkg.2
```

```bash
tanzu cluster list --include-management-cluster -A
Downloading the TKG Bill of Materials (BOM) file from 'projects.registry.vmware.com/tkg/tkg-bom:v2.3.1'
Downloading the TKr Bill of Materials (BOM) file from 'projects.registry.vmware.com/tkg/tkr-bom:v1.26.8_vmware.1-tkg.2'
the old providers folder /home/vmadmin/.config/tanzu/tkg/providers is backed up to /home/vmadmin/.config/tanzu/tkg/providers-20240820201640-5iag6pha
  NAME                          NAMESPACE   STATUS    CONTROLPLANE  WORKERS  KUBERNETES        ROLES       PLAN  TKR
  tkg-dev-cluster               default     running   1/1           4/4      v1.26.8+vmware.1  <none>      dev   v1.26.8---vmware.1-tkg.2
  tkg-preprod-cluster           default     running   3/3           3/3      v1.26.8+vmware.1  <none>      prod  v1.26.8---vmware.1-tkg.2
  tkg-staging-cluster           default     running   1/1           2/2      v1.26.8+vmware.1  <none>      dev   v1.26.8---vmware.1-tkg.2
  tkg-test-cluster              default     running   1/1           2/2      v1.26.8+vmware.1  <none>      dev   v1.26.8---vmware.1-tkg.2
  nprod-tkg-management-cluster  tkg-system  updating  3/3           2/1      v1.27.5+vmware.1  management  prod  v1.27.5---vmware.1-tkg.3
```

### Upgrade Management Cluster

```bash
tanzu mc upgrade -v 7
```

Once the upgrade for management cluster is successful, you can validate the version using

```bash
tanzu cluster list --include-management-cluster -A
  NAME                          NAMESPACE   STATUS   CONTROLPLANE  WORKERS  KUBERNETES        ROLES       PLAN  TKR
  tkg-dev-cluster               default     running  1/1           4/4      v1.27.5+vmware.1  <none>      dev   v1.26.8---vmware.1-tkg.2
  tkg-preprod-cluster           default     running  3/3           3/3      v1.26.8+vmware.1  <none>      prod  v1.26.8---vmware.1-tkg.2
  tkg-staging-cluster           default     running  1/1           2/2      v1.26.8+vmware.1  <none>      dev   v1.26.8---vmware.1-tkg.2
  tkg-test-cluster              default     running  1/1           2/2      v1.26.8+vmware.1  <none>      dev   v1.26.8---vmware.1-tkg.2
  nprod-tkg-management-cluster  tkg-system  running  3/3           1/1      v1.27.5+vmware.1  management  prod  v1.27.5---vmware.1-tkg.3
```

### Upgrade Workload Clusters

#### Pre Validation

```bash
kg nodes -A --context tkg-dev-cluster-admin@tkg-dev-cluster
E0820 20:21:17.118756 1751471 memcache.go:287] couldn't get resource list for packages.operators.coreos.com/v1: the server is currently unable to handle the request
E0820 20:21:17.126237 1751471 memcache.go:121] couldn't get resource list for packages.operators.coreos.com/v1: the server is currently unable to handle the request
E0820 20:21:17.133786 1751471 memcache.go:121] couldn't get resource list for packages.operators.coreos.com/v1: the server is currently unable to handle the request
E0820 20:21:17.140922 1751471 memcache.go:121] couldn't get resource list for packages.operators.coreos.com/v1: the server is currently unable to handle the request
NAME                                          STATUS   ROLES           AGE    VERSION
tkg-dev-cluster-control-plane-gmjd7           Ready    control-plane   138d   v1.26.8+vmware.1
tkg-dev-cluster-md-0-64dbb4875dxxck9p-62772   Ready    <none>          138d   v1.26.8+vmware.1
tkg-dev-cluster-md-0-64dbb4875dxxck9p-bt466   Ready    <none>          138d   v1.26.8+vmware.1
tkg-dev-cluster-md-0-64dbb4875dxxck9p-mwttd   Ready    <none>          138d   v1.26.8+vmware.1
tkg-dev-cluster-md-0-64dbb4875dxxck9p-xfbh7   Ready    <none>          138d   v1.26.8+vmware.1
```

```bash
kg pods -A --context tkg-dev-cluster-admin@tkg-dev-cluster
E0820 20:20:55.425842 1751454 memcache.go:287] couldn't get resource list for packages.operators.coreos.com/v1: the server is currently unable to handle the request
E0820 20:20:55.434717 1751454 memcache.go:121] couldn't get resource list for packages.operators.coreos.com/v1: the server is currently unable to handle the request
E0820 20:20:55.447212 1751454 memcache.go:121] couldn't get resource list for packages.operators.coreos.com/v1: the server is currently unable to handle the request
E0820 20:20:55.453517 1751454 memcache.go:121] couldn't get resource list for packages.operators.coreos.com/v1: the server is currently unable to handle the request
NAMESPACE              NAME                                                            READY   STATUS             RESTARTS            AGE
avi-system             ako-0                                                           1/1     Running            2 (71d ago)         109d
baas-dev               baas-integrations-548db6c477-bmn8x                              1/1     Running            0                   32h
baas-dev               baas-logs-checker-28735940-sld9j                                0/1     Completed          0                   55s
baas-dev               backoffice-app-7c87c5c48-68b4k                                  1/1     Running            0                   29d
baas-dev               boutil-app-599cf49d5-t4spz                                      1/1     Running            0                   46d
baas-dev               cms-app-595b77696-qkv7d                                         1/1     Running            0                   26d
baas-dev               ekyc-service-556949c9d8-cv5rf                                   0/1     ImagePullBackOff   0                   46d
baas-dev               ekyc-service-b9645c4fd-9s62c                                    1/1     Running            0                   138d
baas-dev               enterprise-app-5499ff884-7x5tt                                  1/1     Running            0                   46d
baas-dev               gateway-api-784f55457b-6hp8n                                    1/1     Running            0                   5d6h
baas-dev               hobs-app-7bfb75f868-69b7h                                       1/1     Running            0                   27d
baas-dev               ibas-app-7d7fb94456-2k5bn                                       1/1     Running            0                   164m
baas-dev               kong-6f44999fb7-dmrbs                                           1/1     Running            0                   46d
baas-dev               konga-5c678cfdc4-878hr                                          1/1     Running            0                   46d
baas-dev               lead-service-6688d6c84-xppjf                                    1/1     Running            0                   42d
baas-dev               notifications-app-7688d7f455-qfmhz                              1/1     Running            0                   46d
baas-dev               notifications-rebuild-app-f6c78d4b6-75m5d                       1/1     Running            0                   34d
baas-dev               notifications-system-75f5bfc8fb-cgpst                           1/1     Running            0                   46d
baas-dev               openapi-app-789c7889c6-8wb72                                    1/1     Running            0                   29h
baas-dev               otp-app-cd8595ffb-b9jvp                                         1/1     Running            0                   46d
baas-dev               payments-app-bfd65bfdc-dz8sn                                    1/1     Running            0                   46d
baas-dev               sales-app-5cc7667bdf-52p8g                                      1/1     Running            0                   39d
baas-dev               shopping-cart-5dfb7789cb-k45ln                                  1/1     Running            0                   46d
baas-dev               ticket-app-8568449d9d-jrmvr                                     1/1     Running            0                   43d
backoffice-dev         backoffice-web-8484cc68b-dl7k2                                  1/1     Running            0                   23d
bidafiber-dev          bidafiber-web-b8547db6-wwg5l                                    1/1     Running            0                   138d
bidafiber-dev          bidafiber-web-v2-58598bbffc-mj69m                               1/1     Running            0                   4d2h
bidafiber-dev          external-secrets-7f4c979df7-4mxcp                               1/1     Running            0                   138d
bidafiber-dev          external-secrets-cert-controller-dcf98465-lvbwv                 1/1     Running            0                   138d
bidafiber-dev          external-secrets-webhook-66b857759c-qw428                       1/1     Running            0                   138d
cert-manager           cert-manager-6d857c9f47-h9qhx                                   1/1     Running            0                   138d
cert-manager           cert-manager-cainjector-657c456698-4pd8n                        1/1     Running            0                   138d
cert-manager           cert-manager-webhook-5f8d955b8f-tvbd2                           1/1     Running            0                   138d
database               mysql-5f85654d75-btx7d                                          1/1     Running            0                   138d
directus-dev           database-7c4799c8d-25kdn                                        1/1     Running            0                   76d
enterprise             enterprise-portal-deployment-79f7766c48-95jxj                   1/1     Running            0                   138d
gofiber-dev            gofiber-web-8678bff7bf-v56tl                                    1/1     Running            0                   4d8h
iam-dev                accounts-f744fcfd8-vpfqx                                        1/1     Running            0                   22d
iam-dev                keycloak-74cd5fcd4d-zm2zr                                       1/1     Running            0                   22d
iam                    accounts-66888c4bfc-psj4k                                       0/1     ImagePullBackOff   0                   61d
jupyter                jupyter-74df795df9-5lt7f                                        1/1     Running            0                   138d
kube-system            antrea-agent-2mc6b                                              2/2     Running            0                   138d
kube-system            antrea-agent-5jsxd                                              2/2     Running            3 (120d ago)        138d
kube-system            antrea-agent-lg868                                              2/2     Running            0                   138d
kube-system            antrea-agent-mgmzt                                              2/2     Running            0                   138d
kube-system            antrea-agent-xrr46                                              2/2     Running            0                   138d
kube-system            antrea-controller-79cc497d75-5crf7                              1/1     Running            0                   138d
kube-system            coredns-868fb44cc5-5p5jv                                        1/1     Running            0                   138d
kube-system            coredns-868fb44cc5-lwckv                                        1/1     Running            1 (120d ago)        138d
kube-system            etcd-tkg-dev-cluster-control-plane-gmjd7                        1/1     Running            0                   138d
kube-system            kube-apiserver-tkg-dev-cluster-control-plane-gmjd7              1/1     Running            0                   138d
kube-system            kube-controller-manager-tkg-dev-cluster-control-plane-gmjd7     1/1     Running            1 (90d ago)         138d
kube-system            kube-proxy-7z6dx                                                1/1     Running            0                   138d
kube-system            kube-proxy-d976m                                                1/1     Running            0                   138d
kube-system            kube-proxy-n26fz                                                1/1     Running            0                   138d
kube-system            kube-proxy-n9ml2                                                1/1     Running            0                   138d
kube-system            kube-proxy-rwg5l                                                1/1     Running            1 (120d ago)        138d
kube-system            kube-scheduler-tkg-dev-cluster-control-plane-gmjd7              1/1     Running            1 (90d ago)         138d
kube-system            kube-vip-tkg-dev-cluster-control-plane-gmjd7                    1/1     Running            0                   138d
kube-system            kube-vip-tkg-dev-cluster-control-plane-mmpb4                    1/1     Terminating        7 (138d ago)        174d
kube-system            metrics-server-8f4666b6b-ds2lb                                  1/1     Running            0                   138d
kube-system            vsphere-cloud-controller-manager-xl5sk                          1/1     Running            1 (90d ago)         138d
miniprojects           cxp-api-79fb54cbb4-vmlgj                                        1/1     Running            1 (120d ago)        138d
miniprojects           todo-api-f4dbfcd7f-w2b7w                                        0/1     CrashLoopBackOff   38168 (3m52s ago)   138d
monitoring             alertmanager-kube-prometheus-stack-alertmanager-0               2/2     Running            0                   48d
monitoring             kube-prometheus-stack-kube-state-metrics-654c744f4c-tlgk7       1/1     Running            0                   48d
monitoring             kube-prometheus-stack-operator-b48dc4d6c-flq9d                  1/1     Running            0                   48d
monitoring             kube-prometheus-stack-prometheus-node-exporter-2s9fs            1/1     Running            0                   48d
monitoring             kube-prometheus-stack-prometheus-node-exporter-cgpzz            1/1     Running            0                   48d
monitoring             kube-prometheus-stack-prometheus-node-exporter-r5tkq            1/1     Running            0                   48d
monitoring             kube-prometheus-stack-prometheus-node-exporter-t4lwv            1/1     Running            0                   48d
monitoring             kube-prometheus-stack-prometheus-node-exporter-zq7mg            1/1     Running            0                   48d
monitoring             prometheus-kube-prometheus-stack-prometheus-0                   2/2     Running            0                   48d
notification-dev       notification-portal-deployment-64ffb9c879-8gzb4                 1/1     Running            0                   138d
olm                    catalog-operator-69f7ffbc74-9h7j7                               1/1     Running            0                   138d
olm                    olm-operator-d6d858764-9vbfh                                    1/1     Running            7 (15d ago)         138d
olm                    operatorhubio-catalog-57jbq                                     1/1     Running            0                   17h
olm                    packageserver-68b6c4c69d-6jmr5                                  1/1     Running            0                   138d
olm                    packageserver-68b6c4c69d-g92sf                                  1/1     Running            0                   138d
pinniped-concierge     pinniped-concierge-5d989cc4b6-b2c9d                             1/1     Running            0                   134d
pinniped-concierge     pinniped-concierge-5d989cc4b6-lmlkk                             1/1     Running            0                   134d
pinniped-concierge     pinniped-concierge-kube-cert-agent-f46864db6-jvz69              1/1     Running            0                   134d
prereg-dev             cpe-api-675749fddb-9jwhq                                        1/1     Running            1 (120d ago)        138d
prereg-dev             cpe-api-675749fddb-fx6fj                                        1/1     Running            0                   138d
prereg-dev             preregistration-deployment-76cd6f5cff-bn6p7                     0/1     CrashLoopBackOff   37751 (2m4s ago)    138d
pwm                    pwm-7444567649-wsrcc                                            1/1     Running            0                   138d
rabbitmq               rabbitmq-6dc49c775f-7sx8n                                       1/1     Running            0                   138d
redis-dev              redis-master-0                                                  1/1     Running            0                   138d
redis-dev              redis-replicas-0                                                1/1     Running            1 (120d ago)        138d
redis-dev              redis-replicas-1                                                1/1     Running            1 (120d ago)        138d
redis-dev              redis-replicas-2                                                1/1     Running            0                   138d
superset               superset-6b595cb98c-n9cxn                                       1/1     Running            0                   138d
superset               superset-postgresql-0                                           1/1     Running            1 (120d ago)        138d
superset               superset-redis-master-0                                         1/1     Running            0                   138d
superset               superset-worker-5fb974c999-c2vhz                                1/1     Running            1 (120d ago)        138d
tanzu-system-ingress   contour-7455c94764-78lbr                                        1/1     Running            0                   138d
tanzu-system-ingress   contour-7455c94764-f2l6h                                        1/1     Running            1 (90d ago)         138d
tanzu-system-ingress   envoy-m5df7                                                     2/2     Running            0                   138d
tanzu-system-ingress   envoy-n9mnx                                                     2/2     Running            0                   138d
tanzu-system-ingress   envoy-pgmdl                                                     2/2     Running            2 (120d ago)        138d
tanzu-system-ingress   envoy-rk6bv                                                     2/2     Running            0                   138d
tanzu-system-logging   fluent-bit-2g5f9                                                1/1     Running            0                   12d
tanzu-system-logging   fluent-bit-dzbmm                                                1/1     Running            0                   12d
tanzu-system-logging   fluent-bit-ltqdc                                                1/1     Running            0                   12d
tanzu-system-logging   fluent-bit-sjzqq                                                1/1     Running            0                   12d
tanzu-system-logging   fluent-bit-xf56c                                                1/1     Running            0                   12d
tkg-system             kapp-controller-699986bcf-ctjwt                                 2/2     Running            0                   74d
tkg-system             tanzu-capabilities-controller-manager-6ff97656b8-vt5pq          1/1     Running            0                   138d
tracing                springboot-api-d4c7d5f89-vjphd                                  1/1     Running            0                   138d
tracing                zipkin-775bb84db9-4ds7z                                         1/1     Running            1 (120d ago)        138d
velero                 node-agent-7sbfq                                                1/1     Running            0                   117d
velero                 node-agent-frgvn                                                1/1     Running            0                   117d
velero                 node-agent-g4vb8                                                1/1     Running            0                   117d
velero                 node-agent-tb8js                                                1/1     Running            0                   117d
velero                 node-agent-z7hgm                                                1/1     Running            0                   117d
velero                 velero-78c94ff7d6-7ns9k                                         1/1     Running            0                   117d
vmware-system-csi      vsphere-csi-controller-8f5dd5d45-ncpfw                          7/7     Running            55 (13d ago)        138d
vmware-system-csi      vsphere-csi-node-52llf                                          3/3     Running            0                   138d
vmware-system-csi      vsphere-csi-node-k2rzx                                          3/3     Running            0                   138d
vmware-system-csi      vsphere-csi-node-l8vk4                                          3/3     Running            0                   138d
vmware-system-csi      vsphere-csi-node-vqrxg                                          3/3     Running            5 (120d ago)        138d
vmware-system-csi      vsphere-csi-node-zj5hr                                          3/3     Running            2 (138d ago)        138d
vmware-system-tmc      agent-updater-6bb5967679-w45pc                                  1/1     Running            0                   48d
vmware-system-tmc      agentupdater-workload-28735940-nnqhl                            0/1     Completed          0                   55s
vmware-system-tmc      cluster-auth-pinniped-d966d58cd-7cxpw                           1/1     Running            0                   19d
vmware-system-tmc      cluster-auth-pinniped-d966d58cd-ndqcb                           1/1     Running            0                   19d
vmware-system-tmc      cluster-auth-pinniped-impersonation-proxy-token-cleanup-xzl8p   0/1     Completed          0                   39d
vmware-system-tmc      cluster-auth-pinniped-kube-cert-agent-69655c4f9-r44bn           1/1     Running            0                   19d
vmware-system-tmc      cluster-health-extension-7c4b7948dc-rq4t7                       1/1     Running            0                   19d
vmware-system-tmc      cluster-secret-57967cb95-4jdwd                                  1/1     Running            0                   19d
vmware-system-tmc      data-protection-85765cc5c6-zcf9z                                1/1     Running            0                   85d
vmware-system-tmc      extension-manager-c5f8c86d6-r9kls                               1/1     Running            0                   48d
vmware-system-tmc      extension-updater-68949b949b-v7j89                              1/1     Running            1 (3d14h ago)       19d
vmware-system-tmc      gatekeeper-operator-manager-69f7f59874-fxmrg                    1/1     Running            0                   18d
vmware-system-tmc      inspection-extension-578489f7df-k2njn                           1/1     Running            0                   38d
vmware-system-tmc      intent-agent-6557f56b97-dvvpj                                   1/1     Running            0                   19d
vmware-system-tmc      package-deployment-5f4775658d-flfxn                             1/1     Running            0                   19d
vmware-system-tmc      policy-insight-extension-manager-66f4595975-z986q               1/1     Running            0                   19d
vmware-system-tmc      policy-sync-extension-759844cbf9-msk52                          1/1     Running            0                   19d
vmware-system-tmc      sync-agent-5b4cb54df9-5ml98                                     1/1     Running            1 (120d ago)        125d
vmware-system-tmc      tmc-observer-7dc9499bbc-v5j7g                                   1/1     Running            1 (120d ago)        138d
```

To list your workload clusters, run:

```bash
tanzu cluster list --include-management-cluster -A
  NAME                          NAMESPACE   STATUS   CONTROLPLANE  WORKERS  KUBERNETES        ROLES       PLAN  TKR
  tkg-dev-cluster               default     running  1/1           4/4      v1.27.5+vmware.1  <none>      dev   v1.26.8---vmware.1-tkg.2
  tkg-preprod-cluster           default     running  3/3           3/3      v1.26.8+vmware.1  <none>      prod  v1.26.8---vmware.1-tkg.2
  tkg-staging-cluster           default     running  1/1           2/2      v1.26.8+vmware.1  <none>      dev   v1.26.8---vmware.1-tkg.2
  tkg-test-cluster              default     running  1/1           2/2      v1.26.8+vmware.1  <none>      dev   v1.26.8---vmware.1-tkg.2
  nprod-tkg-management-cluster  tkg-system  running  3/3           1/1      v1.27.5+vmware.1  management  prod  v1.27.5---vmware.1-tkg.3
```

To discover which versions of Kubernetes are made available by a management cluster, run the tanzu kubernetes-release get command:

```bash
tanzu kubernetes-release get

  NAME                       VERSION                  COMPATIBLE  ACTIVE  UPDATES AVAILABLE
  v1.22.17---vmware.1-tkg.2  v1.22.17+vmware.1-tkg.2  True        True
  v1.23.16---vmware.1-tkg.2  v1.23.16+vmware.1-tkg.2  True        True
  v1.24.10---vmware.1-tkg.2  v1.24.10+vmware.1-tkg.2  True        True
```

To discover the newer tkr versions to which you can upgrade a workload cluster running an older tkr version, run the tanzu kubernetes-release available-upgradesget command, specifying the current tkr version:

```bash
tanzu cluster available-upgrades get {{ CLUSTER_NAME }}
  v1.26.8---vmware.1-tkg.4         v1.26.8+vmware.1-tkg.4         True
  v1.27.5---vmware.1-tiny.1-tkg.3  v1.27.5+vmware.1-tiny.1-tkg.3  True
  v1.27.5---vmware.1-tkg.3         v1.27.5+vmware.1-tkg.3         True
```

To upgrade workload cluster

```bash
tanzu cluster upgrade {{ CLUSTER-NAME }} --tkr v1.27.5 -v 9
```

Once the upgrade is successful, validate the status using

```bash
tanzu cluster list --include-management-cluster -A
  NAME                          NAMESPACE   STATUS   CONTROLPLANE  WORKERS  KUBERNETES        ROLES       PLAN  TKR
  tkg-dev-cluster               default     running  1/1           4/4      v1.27.5+vmware.1  <none>      dev   v1.27.5---vmware.1-tkg.3
  tkg-preprod-cluster           default     running  3/3           3/3      v1.26.8+vmware.1  <none>      prod  v1.26.8---vmware.1-tkg.2
  tkg-staging-cluster           default     running  1/1           2/2      v1.26.8+vmware.1  <none>      dev   v1.26.8---vmware.1-tkg.2
  tkg-test-cluster              default     running  1/1           2/2      v1.26.8+vmware.1  <none>      dev   v1.26.8---vmware.1-tkg.2
  nprod-tkg-management-cluster  tkg-system  running  3/3           1/1      v1.27.5+vmware.1  management  prod  v1.27.5---vmware.1-tkg.3
```
