# Upgrade Managment Cluster to 2.1.1

- [Upgrade Managment Cluster to 2.1.1](#upgrade-managment-cluster-to-211)
    - [Make sure you are setting right context](#make-sure-you-are-setting-right-context)
    - [In above case we are upgrading non-prod management cluster](#in-above-case-we-are-upgrading-non-prod-management-cluster)
    - [Get the current tkg version that was deployed](#get-the-current-tkg-version-that-was-deployed)
      - [List the Plugins available](#list-the-plugins-available)
    - [Install  management-cluster Plugin](#install--management-cluster-plugin)
    - [Check CLI for management-cluster options](#check-cli-for-management-cluster-options)
    - [Upgrade Management Cluster](#upgrade-management-cluster)
    - [If upgrade fails](#if-upgrade-fails)
      - [Check the default limits for the tkr-source-controller](#check-the-default-limits-for-the-tkr-source-controller)
      - [Pause reconciliation of both "tkg-pkg" and "tkr-source-controller"](#pause-reconciliation-of-both-tkg-pkg-and-tkr-source-controller)
    - [Perform the upgrade Again](#perform-the-upgrade-again)
      - [Once upgrade is successful](#once-upgrade-is-successful)
    - [List nodes of Management Cluster](#list-nodes-of-management-cluster)
    - [Get the current tkg version that was deployed](#get-the-current-tkg-version-that-was-deployed-1)

### Make sure you are setting right context

```bash
contexts
CURRENT   NAME                                                                  CLUSTER                         AUTHINFO                                 NAMESPACE
          mirror-tkg-management-cluster-admin@mirror-tkg-management-cluster     mirror-tkg-management-cluster   mirror-tkg-management-cluster-admin
*         nprod-tkg-management-cluster-admin@nprod-tkg-management-cluster       nprod-tkg-management-cluster    nprod-tkg-management-cluster-admin
          prod-tkg-management-cluster-admin@prod-tkg-management-cluster         prod-tkg-management-cluster     prod-tkg-management-cluster-admin
          tanzu-cli-nprod-tkg-management-cluster@nprod-tkg-management-cluster   nprod-tkg-management-cluster    tanzu-cli-nprod-tkg-management-cluster
          tanzu-cli-tkg-mirror-cluster@tkg-mirror-cluster                       tkg-mirror-cluster              tanzu-cli-tkg-mirror-cluster
          tkg-dev-cluster-admin@tkg-dev-cluster                                 tkg-dev-cluster                 tkg-dev-cluster-admin
          tkg-mirror-cluster-admin@tkg-mirror-cluster                           tkg-mirror-cluster              tkg-mirror-cluster-admin
          tkg-preprod-cluster-admin@tkg-preprod-cluster                         tkg-preprod-cluster             tkg-preprod-cluster-admin
          tkg-prod-cluster-admin@tkg-prod-cluster                               tkg-prod-cluster                tkg-prod-cluster-admin
          tkg-shared-service-cluster-admin@tkg-shared-service-cluster           tkg-shared-service-cluster      tkg-shared-service-cluster-admin
          tkg-staging-cluster-admin@tkg-staging-cluster                         tkg-staging-cluster             tkg-staging-cluster-admin
          tkg-test-cluster-admin@tkg-test-cluster                               tkg-test-cluster                tkg-test-cluster-admin
          tkg-uat-cluster-admin@tkg-uat-cluster
```

### In above case we are upgrading non-prod management cluster

```bash
kubectl get cluster -A
NAMESPACE    NAME                           PHASE         AGE     VERSION
default      tkg-dev-cluster                Provisioned   2y78d
default      tkg-preprod-cluster            Provisioned   2y78d
default      tkg-staging-cluster            Provisioned   2y78d
default      tkg-test-cluster               Provisioned   2y78d
default      tkg-uat-cluster                Provisioned   449d
tkg-system   nprod-tkg-management-cluster   Provisioned   2y78d
```

### Get the current tkg version that was deployed

```bash
kubectl get cluster nprod-tkg-management-cluster -n tkg-system -o yaml | grep TKGVERSION
    TKGVERSION: v1.6.1
```

#### List the Plugins available  

```bash
tanzu plugin list
Standalone Plugins
  NAME                DESCRIPTION                                                        TARGET      DISCOVERY  VERSION  STATUS
  isolated-cluster    isolated-cluster operations                                                    default    v0.28.1  not installed
  login               Login to the platform                                                          default    v0.28.1  not installed
  pinniped-auth       Pinniped authentication operations (usually not directly invoked)              default    v0.28.1  not installed
  management-cluster  Kubernetes management-cluster operations                           kubernetes  default    v0.28.1  not installed
  package             Tanzu package management                                           kubernetes  default    v0.28.1  not installed
  secret              Tanzu secret management                                            kubernetes  default    v0.28.1  not installed
  telemetry           Configure cluster-wide telemetry settings                          kubernetes  default    v0.28.1  not installed

Plugins from Context:  nprod-tkg-management-cluster
  NAME                DESCRIPTION                           TARGET      VERSION  STATUS
  cluster             Kubernetes cluster operations         kubernetes  v0.25.4  installed
  feature             Operate on features and featuregates  kubernetes  v0.25.4  installed
  kubernetes-release  Kubernetes release operations         kubernetes  v0.25.4  installed
```

### Install  management-cluster Plugin

```bash
tanzu plugin install management-cluster --version v0.28.1
ℹ  Installing plugin 'management-cluster:v0.28.1' with target 'kubernetes'
✔  successfully installed 'management-cluster' plugin
```

List the Plugins

```bash
tanzu plugin list
Standalone Plugins
  NAME                DESCRIPTION                                                        TARGET      DISCOVERY  VERSION  STATUS
  isolated-cluster    isolated-cluster operations                                                    default    v0.28.1  not installed
  login               Login to the platform                                                          default    v0.28.1  not installed
  pinniped-auth       Pinniped authentication operations (usually not directly invoked)              default    v0.28.1  not installed
  management-cluster  Kubernetes management-cluster operations                           kubernetes  default    v0.28.1  installed
  package             Tanzu package management                                           kubernetes  default    v0.28.1  not installed
  secret              Tanzu secret management                                            kubernetes  default    v0.28.1  not installed
  telemetry           Configure cluster-wide telemetry settings                          kubernetes  default    v0.28.1  not installed

Plugins from Context:  nprod-tkg-management-cluster
  NAME                DESCRIPTION                           TARGET      VERSION  STATUS
  cluster             Kubernetes cluster operations         kubernetes  v0.25.4  installed
  feature             Operate on features and featuregates  kubernetes  v0.25.4  installed
  kubernetes-release  Kubernetes release operations         kubernetes  v0.25.4  installed
```

you should be able to see `management-cluster` installed successfully

### Check CLI for management-cluster options

```bash
tanzu management-cluster --help
Kubernetes management cluster operations

Usage:
  tanzu management-cluster [command]

Aliases:
  management-cluster, mc, management-clusters

Available Commands:
  ceip-participation Get or set ceip participation
  create             Create a Tanzu Kubernetes Grid management cluster
  credentials        Update Credentials for Management Cluster
  delete             Delete a management cluster
  get                Get details about the current management cluster
  kubeconfig         Kubeconfig of management cluster
  osimage            Tanzu TKR image operations for Kubernetes nodes.
  permissions        Configure permissions on cloud providers
  upgrade            Upgrades the management cluster

Flags:
  -h, --help              help for management-cluster
      --log-file string   Log file path
  -v, --verbose int32     Number for the log level verbosity(0-9)

Use "tanzu management-cluster [command] --help" for more information about a command.
```

### Upgrade Management Cluster

```bash
tanzu mc upgrade -v 6
Upgrading management cluster 'nprod-tkg-management-cluster' to TKG version 'v2.1.1' with Kubernetes version 'v1.24.10+vmware.1'. Are you sure? [y/N]: y
Validating the compatibility before management cluster upgrade
Validating for the required environment variables to be set
Validating for the user configuration secret to be existed in the cluster
tkg-pkg-tkg-system-values secret in tkg-system namespace not found, trying to fetch nprod-tkg-management-cluster-config-values secret instead...
Warning: unable to find component 'kube_rbac_proxy' under BoM
Upgrading management cluster providers...
Checking cert-manager version...
Deleting cert-manager Version="v1.7.2"
Installing cert-manager Version="v1.9.1"
Waiting for cert-manager to be available...
Performing upgrade...
Scaling down Provider="cluster-api" Version="" Namespace="capi-system"
Scaling down Provider="bootstrap-kubeadm" Version="" Namespace="capi-kubeadm-bootstrap-system"
Scaling down Provider="control-plane-kubeadm" Version="" Namespace="capi-kubeadm-control-plane-system"
Scaling down Provider="infrastructure-vsphere" Version="" Namespace="capv-system"
Deleting Provider="cluster-api" Version="" Namespace="capi-system"
Installing Provider="cluster-api" Version="v1.2.8" TargetNamespace="capi-system"
I0206 22:02:24.459050   17671 request.go:601] Waited for 1.039671934s due to client-side throttling, not priority and fairness, request: GET:https://172.31.95.204:6443/apis/crd.antrea.io/v1alpha2?timeout=30s
Deleting Provider="bootstrap-kubeadm" Version="" Namespace="capi-kubeadm-bootstrap-system"
Installing Provider="bootstrap-kubeadm" Version="v1.2.8" TargetNamespace="capi-kubeadm-bootstrap-system"
Deleting Provider="control-plane-kubeadm" Version="" Namespace="capi-kubeadm-control-plane-system"
Installing Provider="control-plane-kubeadm" Version="v1.2.8" TargetNamespace="capi-kubeadm-control-plane-system"
Deleting Provider="infrastructure-vsphere" Version="" Namespace="capv-system"
Installing Provider="infrastructure-vsphere" Version="v1.5.3" TargetNamespace="capv-system"
Management cluster providers upgraded successfully...
Preparing addons manager for upgrade
Upgrading kapp-controller...
Adding last-applied annotation on kapp-controller...
Removing old management components...
Upgrading management components...
Pausing lifecycle management for addons-manager
Pausing reconciliation for tkg-system/nprod-tkg-management-cluster-tanzu-addons-manager-addon secret
Pausing reconciliation for tkg-system/tanzu-addons-manager packageinstall
Deleting tkg-system/tanzu-addons-manager packageinstall with noopdelete
Pausing reconciliation for tkg-system/nprod-tkg-management-cluster-ako-operator-addon secret
Pausing reconciliation for tkg-system/ako-operator packageinstall
Deleting tkg-system/ako-operator packageinstall with noopdelete
ℹ   Updating package repository 'tanzu-management'
ℹ   Getting package repository 'tanzu-management'
ℹ   Validating provided settings for the package repository
ℹ   Updating package repository resource
ℹ   Waiting for 'PackageRepository' reconciliation for 'tanzu-management'
ℹ   'PackageRepository' resource install status: Reconciling
ℹ   'PackageRepository' resource install status: ReconcileSucceeded
ℹ  Updated package repository 'tanzu-management' in namespace 'tkg-system'
ℹ   Installing package 'tkg.tanzu.vmware.com'
ℹ   Getting package metadata for 'tkg.tanzu.vmware.com'
ℹ   Creating service account 'tkg-pkg-tkg-system-sa'
ℹ   Creating cluster admin role 'tkg-pkg-tkg-system-cluster-role'
ℹ   Creating cluster role binding 'tkg-pkg-tkg-system-cluster-rolebinding'
ℹ   Creating secret 'tkg-pkg-tkg-system-values'
ℹ   Creating package resource
ℹ   Waiting for 'PackageInstall' reconciliation for 'tkg-pkg'
/ 'PackageInstall' resource install status: Reconciling!

Please consider using 'tanzu package installed update' to update the installed package with correct settings

ℹ   'PackageInstall' resource install status: Reconciling


resource reconciliation failed: I0206 14:04:12.875148   19582 request.go:665] Waited for 1.045348221s due to client-side throttling, not priority and fairness, request: GET:https://100.64.0.1:443/apis/networking.tkg.tanzu.vmware.com/v1alpha1?timeout=32s
kapp: Error: waiting on reconcile packageinstall/tanzu-framework (packaging.carvel.dev/v1alpha1) namespace: tkg-system:
  Finished unsuccessfully (Reconcile failed:  (message: Error (see .status.usefulErrorMessage for details))). Reconcile failed: Error (see .status.usefulErrorMessage for details)
Deleting tkg-system/nprod-tkg-management-cluster-tanzu-addons-manager-addon secret
Cleanup core packages repository...
Deleting core package repository
Upgrading management cluster kubernetes version...
Warning: Use of ytt based cluster templates will be deprecated in favor of ClusterClass templates in a future version of TKG. Please work to move your workloads to a ClusterClass enabled cluster.
Verifying kubernetes version...
Retrieving configuration for upgrade cluster...
Create InfrastructureTemplate for upgrade...
Upgrading control plane nodes...
Patching KubeadmControlPlane with the kubernetes version v1.24.10+vmware.1...
Updating the KCP object with k8s version v1.24.10+vmware.1
Updating Audit APIVerison for KCP
Enabling Pod Security Standard for KCP
Enabling Pod Security Standard for KCP
Waiting for kubernetes version to be updated for control plane nodes
```

repeat this command to see the status of the node updates

```bash
kubectl get nodes
NAME                                                 STATUS                     ROLES                  AGE     VERSION
nprod-tkg-management-cluster-control-plane-jqn2s     Ready                      control-plane,master   313d    v1.23.10+vmware.1
nprod-tkg-management-cluster-control-plane-k8bvn     Ready                      control-plane,master   313d    v1.23.10+vmware.1
nprod-tkg-management-cluster-control-plane-prswj     Ready,SchedulingDisabled   control-plane,master   313d    v1.23.10+vmware.1
nprod-tkg-management-cluster-control-plane-tlbsn     NotReady                   control-plane          3m37s   v1.24.10+vmware.1
nprod-tkg-management-cluster-md-0-7644b894fc-p6p65   Ready                      <none>                 313d    v1.23.10+vmware.1
```

### If upgrade fails

While Upgrading to 2.1.x, 2.2.x, 2.3.x we have issue with `tkr-source-controller deployment`, this will fail the upgrade.

#### Check the default limits for the tkr-source-controller

```bash
kubectl  describe pod -n tkg-system {{ tkr-source-controller_pod_name }} | egrep -iB1 'cpu|mem'
```

#### Pause reconciliation of both "tkg-pkg" and "tkr-source-controller"

```bash
kubectl patch pkgi -n tkg-system tkg-pkg -p '{"spec":{"paused":true}}' --type=merge
kubectl patch pkgi -n tkg-system tkr-source-controller -p '{"spec":{"paused":true}}' --type=merge
```

Modify the tkr-source-controller deployment with increased memory limits:

```bash
kubectl edit deployments.apps -n tkg-system tkr-source-controller-manager
```

Below are the recommended changes to the pod memory limits:

```
Limits:
  cpu:     500m
  memory:  600Mi
Requests:
  cpu:        200m
  memory:     200Mi
```

Once the changes are applied validate the changes are reflecting on pod

```bash
kubectl  describe pod -n tkg-system {{ tkr-source-controller_pod_name }} | egrep -iB1 'cpu|mem'
```

List the pods that are running in `tkg-system` and make sure `tkr-source-controller` pod is in running state

**Reference Link:**

<https://kb.vmware.com/s/article/92524>

### Perform the upgrade Again

```bash
tanzu mc upgrade -v 6
```

#### Once upgrade is successful

```bash

management cluster is opted out of telemetry - skipping telemetry image upgrade
Applying patch to resource nprod-tkg-management-cluster of type *v1beta1.Cluster ...
Creating tkg-bom versioned ConfigMaps...
Management cluster 'nprod-tkg-management-cluster' successfully upgraded to TKG version 'v2.1.1' with kubernetes version 'v1.24.10+vmware.1'
ℹ  Checking for required plugins...
ℹ  Installing plugin 'telemetry:v0.28.1' with target 'kubernetes'
ℹ  Installing plugin 'kubernetes-release:v0.28.1' with target 'kubernetes'
ℹ  Installing plugin 'package:v0.28.1' with target 'kubernetes'
ℹ  Installing plugin 'pinniped-auth:v0.28.1'
ℹ  Installing plugin 'secret:v0.28.1' with target 'kubernetes'
ℹ  Installing plugin 'cluster:v0.28.1' with target 'kubernetes'
ℹ  Installing plugin 'feature:v0.28.1' with target 'kubernetes'
ℹ  Installing plugin 'isolated-cluster:v0.28.1'
ℹ  Installing plugin 'login:v0.28.1'
ℹ  Successfully installed all required plugins

```

### List nodes of Management Cluster

```bash
kubectl get nodes
NAME                                                 STATUS                     ROLES           AGE    VERSION
nprod-tkg-management-cluster-control-plane-229s4     Ready                      control-plane   153m   v1.24.10+vmware.1
nprod-tkg-management-cluster-control-plane-tlbsn     Ready                      control-plane   159m   v1.24.10+vmware.1
nprod-tkg-management-cluster-control-plane-wp6ll     Ready                      control-plane   149m   v1.24.10+vmware.1
nprod-tkg-management-cluster-md-0-5947974df5-m6w8v   Ready                      <none>          19m    v1.24.10+vmware.1
```

confirm all the nodes are in running state.

### Get the current tkg version that was deployed

```bash
kubectl get cluster nprod-tkg-management-cluster -n tkg-system -o yaml | grep TKGVERSION
    TKGVERSION: vkubectl get1
```

List the clusters and it's state.

```bash
kg cluster -A
NAMESPACE    NAME                           PHASE         AGE     VERSION
default      tkg-dev-cluster                Provisioned   2y78d
default      tkg-preprod-cluster            Provisioned   2y78d
default      tkg-staging-cluster            Provisioned   2y78d
default      tkg-test-cluster               Provisioned   2y78d
default      tkg-uat-cluster                Provisioned   449d
tkg-system   nprod-tkg-management-cluster   Provisioned   2y78d
```
