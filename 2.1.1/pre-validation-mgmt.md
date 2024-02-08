## Pre validation before upgrading Management Cluster

- [Pre validation before upgrading Management Cluster](#pre-validation-before-upgrading-management-cluster)
  - [Login to Management Cluster](#login-to-management-cluster)
  - [List all workload clusters available in management cluster](#list-all-workload-clusters-available-in-management-cluster)
  - [Make sure your kubect context set to Management Cluster](#make-sure-your-kubect-context-set-to-management-cluster)
  - [List Nodes in Management Cluster](#list-nodes-in-management-cluster)
  - [List all the pods available in management cluster](#list-all-the-pods-available-in-management-cluster)

### Login to Management Cluster

```bash
tanzu login
? Select a server nprod-tkg-management-cluster()
✔  successfully logged in to management cluster using the kubeconfig nprod-tkg-management-cluster
Checking for required plugins...
All required plugins are already installed and up-to-date
```

### List all workload clusters available in management cluster

```bash
tanzu cluster list
  NAME                 NAMESPACE  STATUS         CONTROLPLANE  WORKERS  KUBERNETES         ROLES   PLAN  TKR
  tkg-dev-cluster      default    running        1/1           4/4      v1.23.10+vmware.1  <none>  dev   v1.23.10---vmware.1-tkg.1
  tkg-preprod-cluster  default    running        3/3           3/3      v1.23.10+vmware.1  <none>  prod  v1.23.10---vmware.1-tkg.1
  tkg-staging-cluster  default    running        1/1           2/2      v1.23.10+vmware.1  <none>  dev   v1.23.10---vmware.1-tkg.1
  tkg-test-cluster     default    running        1/1           2/2      v1.23.10+vmware.1  <none>  dev   v1.23.10---vmware.1-tkg.1
  tkg-uat-cluster      default    createStalled  0/1           0/1      v1.21.2+vmware.1   <none>  dev   v1.21.2---vmware.1-tkg.1

```

### Make sure your kubect context set to Management Cluster

```bash
contexts
CURRENT   NAME                                                                  CLUSTER                         AUTHINFO                                 NAMESPACE
          mirror-tkg-management-cluster-admin@mirror-tkg-management-cluster     mirror-tkg-management-cluster   mirror-tkg-management-cluster-admin
          nprod-tkg-management-cluster-admin@nprod-tkg-management-cluster       nprod-tkg-management-cluster    nprod-tkg-management-cluster-admin
          prod-tkg-management-cluster-admin@prod-tkg-management-cluster         prod-tkg-management-cluster     prod-tkg-management-cluster-admin
          tanzu-cli-nprod-tkg-management-cluster@nprod-tkg-management-cluster   nprod-tkg-management-cluster    tanzu-cli-nprod-tkg-management-cluster
          tanzu-cli-tkg-mirror-cluster@tkg-mirror-cluster                       tkg-mirror-cluster              tanzu-cli-tkg-mirror-cluster
          tkg-dev-cluster-admin@tkg-dev-cluster                                 tkg-dev-cluster                 tkg-dev-cluster-admin
          tkg-mirror-cluster-admin@tkg-mirror-cluster                           tkg-mirror-cluster              tkg-mirror-cluster-admin
          tkg-preprod-cluster-admin@tkg-preprod-cluster                         tkg-preprod-cluster             tkg-preprod-cluster-admin
          tkg-prod-cluster-admin@tkg-prod-cluster                               tkg-prod-cluster                tkg-prod-cluster-admin
*         tkg-shared-service-cluster-admin@tkg-shared-service-cluster           tkg-shared-service-cluster      tkg-shared-service-cluster-admin
          tkg-staging-cluster-admin@tkg-staging-cluster                         tkg-staging-cluster             tkg-staging-cluster-admin
          tkg-test-cluster-admin@tkg-test-cluster                               tkg-test-cluster                tkg-test-cluster-admin
          tkg-uat-cluster-admin@tkg-uat-cluster                                 tkg-uat-cluster                 tkg-uat-cluster-admin
kubectl config use-context nprod-tkg-management-cluster-admin@nprod-tkg-management-cluster
Switched to context "nprod-tkg-management-cluster-admin@nprod-tkg-management-cluster".
```

### List Nodes in Management Cluster

```bash
kubectl get nodes
NAME                                                 STATUS   ROLES                  AGE    VERSION
nprod-tkg-management-cluster-control-plane-jqn2s     Ready    control-plane,master   313d   v1.23.10+vmware.1
nprod-tkg-management-cluster-control-plane-k8bvn     Ready    control-plane,master   313d   v1.23.10+vmware.1
nprod-tkg-management-cluster-control-plane-prswj     Ready    control-plane,master   313d   v1.23.10+vmware.1
nprod-tkg-management-cluster-md-0-7644b894fc-p6p65   Ready    <none>                 313d   v1.23.10+vmware.1
```

### List all the pods available in management cluster

```bash
allpods
NAMESPACE                           NAME                                                                       READY   STATUS             RESTARTS            AGE
avi-system                          ako-0                                                                      1/1     Running            1 (15d ago)         257d
capi-kubeadm-bootstrap-system       capi-kubeadm-bootstrap-controller-manager-7dcbb6895f-bz2z2                 1/1     Running            4 (24d ago)         95d
capi-kubeadm-control-plane-system   capi-kubeadm-control-plane-controller-manager-b9f9dcf68-56hd8              1/1     Running            2 (24d ago)         95d
capi-system                         capi-controller-manager-599b888f49-k4m5s                                   1/1     Running            5 (24d ago)         95d
capv-system                         capv-controller-manager-599fdd8955-874m6                                   1/1     Running            5 (24d ago)         91d
cert-manager                        cert-manager-6c8f978699-snpbh                                              1/1     Running            14 (24d ago)        313d
cert-manager                        cert-manager-cainjector-5f76449f7b-lj4gr                                   1/1     Running            66 (24d ago)        313d
cert-manager                        cert-manager-webhook-789f66cdd7-vsr8q                                      1/1     Running            0                   313d
kube-system                         antrea-agent-25mnl                                                         2/2     Running            0                   313d
kube-system                         antrea-agent-4cvsp                                                         2/2     Running            0                   313d
kube-system                         antrea-agent-dvrxp                                                         2/2     Running            0                   313d
kube-system                         antrea-agent-hgw2f                                                         2/2     Running            1 (313d ago)        313d
kube-system                         antrea-controller-7fdc4c9949-qgwvs                                         1/1     Running            0                   313d
kube-system                         coredns-7f4b78dcb5-2zfxz                                                   1/1     Running            0                   313d
kube-system                         coredns-7f4b78dcb5-fm4s9                                                   1/1     Running            0                   313d
kube-system                         etcd-nprod-tkg-management-cluster-control-plane-jqn2s                      1/1     Running            2 (131d ago)        313d
kube-system                         etcd-nprod-tkg-management-cluster-control-plane-k8bvn                      1/1     Running            1 (131d ago)        313d
kube-system                         etcd-nprod-tkg-management-cluster-control-plane-prswj                      1/1     Running            3 (131d ago)        313d
kube-system                         kube-apiserver-nprod-tkg-management-cluster-control-plane-jqn2s            1/1     Running            3 (131d ago)        313d
kube-system                         kube-apiserver-nprod-tkg-management-cluster-control-plane-k8bvn            1/1     Running            1 (131d ago)        313d
kube-system                         kube-apiserver-nprod-tkg-management-cluster-control-plane-prswj            1/1     Running            4 (131d ago)        313d
kube-system                         kube-controller-manager-nprod-tkg-management-cluster-control-plane-jqn2s   1/1     Running            14 (45d ago)        313d
kube-system                         kube-controller-manager-nprod-tkg-management-cluster-control-plane-k8bvn   1/1     Running            18 (45d ago)        313d
kube-system                         kube-controller-manager-nprod-tkg-management-cluster-control-plane-prswj   1/1     Running            18 (24d ago)        313d
kube-system                         kube-proxy-59wpg                                                           1/1     Running            0                   313d
kube-system                         kube-proxy-j4hws                                                           1/1     Running            0                   313d
kube-system                         kube-proxy-svv5l                                                           1/1     Running            0                   313d
kube-system                         kube-proxy-xrd4d                                                           1/1     Running            0                   313d
kube-system                         kube-scheduler-nprod-tkg-management-cluster-control-plane-jqn2s            1/1     Running            17 (24d ago)        313d
kube-system                         kube-scheduler-nprod-tkg-management-cluster-control-plane-k8bvn            1/1     Running            21 (45d ago)        313d
kube-system                         kube-scheduler-nprod-tkg-management-cluster-control-plane-prswj            1/1     Running            16 (75d ago)        313d
kube-system                         kube-vip-nprod-tkg-management-cluster-control-plane-jqn2s                  1/1     Running            6 (150d ago)        313d
kube-system                         kube-vip-nprod-tkg-management-cluster-control-plane-k8bvn                  1/1     Running            9 (122d ago)        313d
kube-system                         kube-vip-nprod-tkg-management-cluster-control-plane-prswj                  1/1     Running            15 (24d ago)        313d
kube-system                         metrics-server-65c849df49-hjznj                                            1/1     Running            0                   313d
kube-system                         vsphere-cloud-controller-manager-465sf                                     1/1     Running            1 (75d ago)         91d
kube-system                         vsphere-cloud-controller-manager-kvz8b                                     1/1     Running            2 (24d ago)         91d
kube-system                         vsphere-cloud-controller-manager-tdzzr                                     1/1     Running            1 (45d ago)         91d
kube-system                         vsphere-csi-controller-8659d876c-chmzw                                     7/7     Running            84 (24d ago)        313d
kube-system                         vsphere-csi-controller-8659d876c-njptd                                     7/7     Running            79 (24d ago)        313d
kube-system                         vsphere-csi-controller-8659d876c-q24qh                                     7/7     Running            71 (45d ago)        313d
kube-system                         vsphere-csi-node-9gplx                                                     3/3     Running            0                   313d
kube-system                         vsphere-csi-node-fhdxl                                                     3/3     Running            2 (313d ago)        313d
kube-system                         vsphere-csi-node-vcm99                                                     3/3     Running            0                   313d
kube-system                         vsphere-csi-node-x25wf                                                     3/3     Running            0                   313d
pinniped-concierge                  pinniped-concierge-f7dff6cdb-chhns                                         1/1     Running            0                   216d
pinniped-concierge                  pinniped-concierge-f7dff6cdb-fxnck                                         1/1     Running            0                   216d
pinniped-concierge                  pinniped-concierge-kube-cert-agent-787fb487d-gbttw                         1/1     Running            0                   216d
pinniped-supervisor                 pinniped-post-deploy-controller-88bdf9785-26ktz                            1/1     Running            0                   216d
pinniped-supervisor                 pinniped-supervisor-cd4f46cdf-c6ttk                                        1/1     Running            3 (35d ago)         216d
pinniped-supervisor                 pinniped-supervisor-cd4f46cdf-vlv5r                                        1/1     Running            3 (35d ago)         216d
tanzu-system-auth                   dex-6f9759ccf7-9nzsj                                                       1/1     Running            0                   71d
tanzu-system-logging                fluent-bit-m5cb6                                                           1/1     Running            0                   182d
tanzu-system-logging                fluent-bit-qqbhv                                                           1/1     Running            0                   182d
tanzu-system-logging                fluent-bit-vskqd                                                           1/1     Running            0                   182d
tanzu-system-logging                fluent-bit-zj4qj                                                           1/1     Running            0                   182d
tkg-system-networking               ako-operator-controller-manager-86b8d5bd66-mr4xr                           1/1     Running            0                   313d
tkg-system                          kapp-controller-5b8ffb96f8-7db58                                           2/2     Running            4 (122d ago)        313d
tkg-system                          tanzu-addons-controller-manager-df96c4656-4n2lj                            1/1     Running            768 (58m ago)       313d
tkg-system                          tanzu-capabilities-controller-manager-7c5c75c565-mflvm                     1/1     Running            0                   313d
tkg-system                          tanzu-featuregates-controller-manager-6d8bf47c55-499z5                     1/1     Running            0                   313d
tkr-system                          tkr-controller-manager-f46b458fd-vrml7                                     0/1     CrashLoopBackOff   71054 (2m57s ago)   313d
```
