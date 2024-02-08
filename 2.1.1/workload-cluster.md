# Upgrade workload cluster

- [Upgrade workload cluster](#upgrade-workload-cluster)
    - [Pre Upgrade validation](#pre-upgrade-validation)
    - [Upgrade](#upgrade)
    - [List all the pods and Httpproxy in the dev cluster](#list-all-the-pods-and-httpproxy-in-the-dev-cluster)
    - [How to Delete a workload cluster](#how-to-delete-a-workload-cluster)

### Pre Upgrade validation

Before upgrading workload cluster, get the status of clusters that are part of manamagement cluster

```bash
tanzu login
? Select a server nprod-tkg-management-cluster()
✔  successfully logged in to management cluster using the kubeconfig nprod-tkg-management-cluster
Checking for required plugins...
All required plugins are already installed and up-to-date
```

```bash
tanzu cluster list --include-management-cluster -A
  NAME                          NAMESPACE   STATUS         CONTROLPLANE  WORKERS  KUBERNETES         ROLES       PLAN  TKR
  tkg-dev-cluster               default     running        1/1           4/4      v1.23.10+vmware.1  <none>      dev   v1.23.10---vmware.1-tkg.1
  tkg-preprod-cluster           default     running        3/3           3/3      v1.23.10+vmware.1  <none>      prod  v1.23.10---vmware.1-tkg.1
  tkg-staging-cluster           default     running        1/1           2/2      v1.23.10+vmware.1  <none>      dev   v1.23.10---vmware.1-tkg.1
  tkg-test-cluster              default     running        1/1           2/2      v1.23.10+vmware.1  <none>      dev   v1.23.10---vmware.1-tkg.1
  tkg-uat-cluster               default     createStalled  0/1           0/1      v1.21.2+vmware.1   <none>      dev   v1.21.2---vmware.1-tkg.1
  nprod-tkg-management-cluster  tkg-system  running        3/3           1/1      v1.24.10+vmware.1  management  prod  v1.24.10---vmware.1-tkg.2
```

for `dev-cluster` you can see the current `tkr` version deployed is `v1.23.10---vmware.1-tkg.1` once the upgrade is successful tkr version will be `v1.24.10---vmware.1-tkg.2`

Make sure your kubectl context set to management cluster

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
[vmadmin@relprdvjhorb01 2.1.1]$ kubectl config use-context nprod-tkg-management-cluster-admin@nprod-tkg-management-cluster
Switched to context "nprod-tkg-management-cluster-admin@nprod-tkg-management-cluster".
```

List all the pods and Httpproxy and the status

```bash
kubectl get pods -A
NAMESPACE                 NAME                                                          READY   STATUS             RESTARTS       AGE
avi-system                ako-0                                                         1/1     Running            0              13m
baas-dev                  baas-integrations-56b64b94c4-hdbrb                            1/1     Running            0              9m2s
baas-dev                  baas-logs-checker-28453998-mrz9x                              0/1     Completed          0              44s
baas-dev                  backoffice-app-958bb8669-w2lll                                1/1     Running            0              4m42s
baas-dev                  boutil-app-b85f559-qdvvp                                      1/1     Running            0              7m3s
baas-dev                  cms-app-ff494598b-764xx                                       1/1     Running            0              9m2s
baas-dev                  ekyc-service-7d9c74bf9b-96ftw                                 0/1     ImagePullBackOff   0              4m42s
baas-dev                  ekyc-service-b9645c4fd-gg7c5                                  1/1     Running            0              11m
baas-dev                  enterprise-app-66594499f5-g5dpc                               1/1     Running            0              7m6s
baas-dev                  hobs-app-69855c7768-855zr                                     1/1     Running            0              4m41s
baas-dev                  lead-service-5656748967-sjx9b                                 1/1     Running            0              4m42s
baas-dev                  notifications-app-67ccf87d54-fmsjs                            1/1     Running            0              4m42s
baas-dev                  notifications-system-85c7d4dfc6-rwnln                         1/1     Running            0              11m
baas-dev                  openapi-app-5df5c88db8-z5h69                                  1/1     Running            0              7m6s
baas-dev                  payments-app-6487c4b78d-tlsv7                                 1/1     Running            0              11m
baas-dev                  sales-app-9bf9697f6-kcc68                                     1/1     Running            0              7m6s
baas-dev                  ticket-app-54f9cbb77-njswd                                    1/1     Running            0              7m6s
bidafiber-dev             bidafiber-web-744bcf575d-wxtpb                                1/1     Running            0              11m
bidafiber-dev             external-secrets-7f4c979df7-qvd77                             1/1     Running            0              9m2s
bidafiber-dev             external-secrets-cert-controller-dcf98465-tqfzn               1/1     Running            0              9m2s
bidafiber-dev             external-secrets-webhook-66b857759c-vp2tn                     1/1     Running            0              4m42s
cert-manager              cert-manager-6d857c9f47-hk8xm                                 1/1     Running            0              11m
cert-manager              cert-manager-cainjector-657c456698-l8pbw                      1/1     Running            0              11m
cert-manager              cert-manager-webhook-5f8d955b8f-wm2ms                         1/1     Running            0              7m6s
database                  mysql-5f85654d75-x9m7s                                        1/1     Running            0              11m
enterprise                enterprise-portal-deployment-79f7766c48-njvmh                 1/1     Running            0              11m
gateway-dev               backoffice-service-7bdc8d7d5b-xtt5s                           1/1     Running            0              9m2s
gateway-dev               boutility-service-7f44889fc5-d6dkh                            1/1     Running            0              8m59s
gateway-dev               cms-service-c6d7654b6-2ptsz                                   1/1     Running            0              11m
gateway-dev               enterprise-service-d467c8f78-mf2p6                            1/1     Running            0              9m2s
gateway-dev               hobs-service-6b7d9bf479-4jccv                                 1/1     Running            0              4m42s
gateway-dev               kong-566fd6bfc-54xk8                                          1/1     Running            0              9m2s
gateway-dev               konga-7c699df86d-qcpb9                                        1/1     Running            0              4m42s
gateway-dev               lead-service-746cb8b49c-mfwdg                                 1/1     Running            0              4m42s
gateway-dev               notification-service-848d64f547-bzvpz                         1/1     Running            0              4m42s
gateway-dev               notification-system-747d56f5c4-pzmt7                          1/1     Running            0              4m41s
gateway-dev               payment-service-594f5dd87d-dhxhz                              1/1     Running            0              4m41s
gateway-dev               sales-service-7d49bdc49c-7mv95                                1/1     Running            0              9m2s
gateway-dev               ticket-service-565c4ccffb-k47ht                               1/1     Running            0              9m1s
gofiber                   gofiber-web-66d55db4-vqfxb                                    1/1     Running            0              11m
iam-dev                   accounts-864f848676-8vxfl                                     1/1     Running            0              4m41s
iam-dev                   keycloak-7cc78c579f-8dc48                                     1/1     Running            0              7m6s
jupyter                   jupyter-74df795df9-ktqdf                                      1/1     Running            0              9m
kube-system               antrea-agent-54w2r                                            2/2     Running            0              9m33s
kube-system               antrea-agent-9fstj                                            2/2     Running            0              11m
kube-system               antrea-agent-g9lp8                                            2/2     Running            0              7m27s
kube-system               antrea-agent-nz8ll                                            2/2     Running            0              16m
kube-system               antrea-agent-rw79k                                            2/2     Running            0              5m13s
kube-system               antrea-controller-78b98d5b9d-88xr7                            1/1     Running            0              4m40s
kube-system               coredns-65fb98cc57-79z8q                                      1/1     Running            0              9m
kube-system               coredns-65fb98cc57-bdppp                                      1/1     Running            0              11m
kube-system               etcd-tkg-dev-cluster-control-plane-mzrhh                      1/1     Running            0              16m
kube-system               kube-apiserver-tkg-dev-cluster-control-plane-mzrhh            1/1     Running            0              16m
kube-system               kube-controller-manager-tkg-dev-cluster-control-plane-mzrhh   1/1     Running            0              15m
kube-system               kube-proxy-4kwdv                                              1/1     Running            0              11m
kube-system               kube-proxy-hsgws                                              1/1     Running            0              9m33s
kube-system               kube-proxy-m6k6d                                              1/1     Running            0              12m
kube-system               kube-proxy-nxqjc                                              1/1     Running            0              5m13s
kube-system               kube-proxy-w9gqj                                              1/1     Running            0              7m27s
kube-system               kube-scheduler-tkg-dev-cluster-control-plane-mzrhh            1/1     Running            0              16m
kube-system               kube-vip-tkg-dev-cluster-control-plane-mzrhh                  1/1     Running            0              15m
kube-system               metrics-server-8f4666b6b-f6tzd                                1/1     Running            0              11m
kube-system               vsphere-cloud-controller-manager-2x45b                        1/1     Running            0              16m
kube-system               vsphere-csi-controller-54b8fc57d4-b46wz                       7/7     Running            1 (13m ago)    14m
kube-system               vsphere-csi-node-8khwn                                        3/3     Running            0              7m27s
kube-system               vsphere-csi-node-d6lzm                                        3/3     Running            0              11m
kube-system               vsphere-csi-node-qblpd                                        3/3     Running            1 (15m ago)    16m
kube-system               vsphere-csi-node-vtqnr                                        3/3     Running            0              5m13s
kube-system               vsphere-csi-node-wqpw7                                        3/3     Running            0              9m33s
miniprojects              cxp-api-79fb54cbb4-dqwkc                                      1/1     Running            0              4m41s
miniprojects              todo-api-748b7b5694-kk4hx                                     0/1     Error              6 (3m ago)     9m
notification-dev          notification-portal-deployment-64ffb9c879-4rwq9               1/1     Running            0              4m40s
olm                       catalog-operator-69f7ffbc74-c865g                             1/1     Running            0              11m
olm                       olm-operator-d6d858764-fth9m                                  1/1     Running            0              11m
olm                       operatorhubio-catalog-k5zqw                                   1/1     Running            0              7m2s
olm                       packageserver-68b6c4c69d-7tkn2                                1/1     Running            0              11m
olm                       packageserver-68b6c4c69d-ww5v6                                1/1     Running            0              7m6s
pinniped-concierge        pinniped-concierge-6d8f4848b6-bwwct                           1/1     Running            0              11m
pinniped-concierge        pinniped-concierge-6d8f4848b6-fbv89                           1/1     Running            0              7m6s
pinniped-concierge        pinniped-concierge-kube-cert-agent-b65ccd5d-f5snc             1/1     Running            0              14m
prereg-dev                cpe-api-675749fddb-h54h9                                      1/1     Running            0              8m59s
prereg-dev                cpe-api-675749fddb-s9cnf                                      1/1     Running            0              4m40s
prereg-dev                preregistration-deployment-59cd58554-4lqmp                    0/1     CrashLoopBackOff   6 (55s ago)    9m2s
pwm                       pwm-7444567649-vmf4r                                          1/1     Running            0              7m5s
rabbitmq                  rabbitmq-6dc49c775f-2lb8q                                     1/1     Running            0              9m1s
redis-dev                 redis-master-0                                                1/1     Running            0              4m27s
redis-dev                 redis-replicas-0                                              1/1     Running            4 (2m3s ago)   8m51s
redis-dev                 redis-replicas-1                                              1/1     Running            0              4m30s
redis-dev                 redis-replicas-2                                              1/1     Running            4 (114s ago)   5m58s
tanzu-system-dashboards   grafana-74b8b59f94-4z67p                                      2/2     Running            0              11m
tanzu-system-ingress      contour-7455c94764-66bkv                                      1/1     Running            0              11m
tanzu-system-ingress      contour-7455c94764-75dcm                                      1/1     Running            0              7m4s
tanzu-system-ingress      envoy-4xq28                                                   2/2     Running            0              7m6s
tanzu-system-ingress      envoy-794kd                                                   2/2     Running            0              11m
tanzu-system-ingress      envoy-stn42                                                   2/2     Running            0              4m42s
tanzu-system-ingress      envoy-xgzn4                                                   2/2     Running            0              9m2s
tanzu-system-logging      fluent-bit-9rws9                                              1/1     Running            0              16m
tanzu-system-logging      fluent-bit-bjz6p                                              1/1     Running            0              7m27s
tanzu-system-logging      fluent-bit-gqwd5                                              1/1     Running            0              11m
tanzu-system-logging      fluent-bit-h686m                                              1/1     Running            0              9m33s
tanzu-system-logging      fluent-bit-pflnr                                              1/1     Running            0              5m13s
tanzu-system-monitoring   alertmanager-6958d95df-s9gxk                                  1/1     Running            0              11m
tanzu-system-monitoring   prometheus-kube-state-metrics-6459db4f56-frbxt                1/1     Running            0              11m
tanzu-system-monitoring   prometheus-node-exporter-6l6wz                                1/1     Running            0              11m
tanzu-system-monitoring   prometheus-node-exporter-88xld                                1/1     Running            0              16m
tanzu-system-monitoring   prometheus-node-exporter-hbswr                                1/1     Running            0              9m33s
tanzu-system-monitoring   prometheus-node-exporter-sm9dc                                1/1     Running            0              5m13s
tanzu-system-monitoring   prometheus-node-exporter-x9ctm                                1/1     Running            0              7m27s
tanzu-system-monitoring   prometheus-pushgateway-5d9cd8c5c-b45zl                        1/1     Running            0              4m39s
tanzu-system-monitoring   prometheus-server-665987f75b-jndxs                            2/2     Running            0              7m5s
tkg-system                kapp-controller-5fb55c6b69-6pn6p                              2/2     Running            0              9m1s
tkg-system                tanzu-capabilities-controller-manager-6ff97656b8-5tswq        1/1     Running            0              11m
tracing                   springboot-api-d4c7d5f89-76d6c                                1/1     Running            0              7m5s
tracing                   zipkin-857b9bb6fc-lphmm                                       1/1     Running            0              11m
velero                    node-agent-672qk                                              1/1     Running            0              9m2s
velero                    node-agent-7wcl4                                              1/1     Running            0              15m
velero                    node-agent-bxkch                                              1/1     Running            0              11m
velero                    node-agent-d8f72                                              1/1     Running            0              7m6s
velero                    node-agent-m8f7n                                              1/1     Running            0              4m42s
velero                    velero-86b7c54b6b-6ctcx                                       1/1     Running            0              7m4s
vmware-system-tmc         agent-updater-f6487d865-cmtzz                                 1/1     Running            0              7m4s
vmware-system-tmc         agentupdater-workload-28453998-glz2x                          0/1     Completed          0              59s
vmware-system-tmc         cluster-auth-pinniped-5cd96898b9-5wm9g                        1/1     Running            0              11m
vmware-system-tmc         cluster-auth-pinniped-5cd96898b9-d6zpw                        1/1     Running            0              7m5s
vmware-system-tmc         cluster-auth-pinniped-kube-cert-agent-68679d5687-pdk6p        1/1     Running            0              12m
vmware-system-tmc         cluster-health-extension-56b94bdbdd-76cqc                     1/1     Running            0              11m
vmware-system-tmc         cluster-secret-78cc799d5f-74ncq                               1/1     Running            0              4m39s
vmware-system-tmc         data-protection-6fc85c8c7-w7hxc                               1/1     Running            0              7m4s
vmware-system-tmc         extension-manager-69dc6995d7-2vkll                            1/1     Running            0              7m4s
vmware-system-tmc         extension-updater-9d6c6bc6c-jggzp                             1/1     Running            0              4m40s
vmware-system-tmc         gatekeeper-operator-manager-746d8fb49f-wbp8h                  1/1     Running            0              7m3s
vmware-system-tmc         inspection-extension-679679456f-wbfh9                         1/1     Running            0              9m1s
vmware-system-tmc         intent-agent-76d847976f-qwvhr                                 1/1     Running            0              4m39s
vmware-system-tmc         package-deployment-f4d976677-9rthb                            1/1     Running            0              7m5s
vmware-system-tmc         policy-insight-extension-manager-9d8f886c8-gk8zs              1/1     Running            0              9m
vmware-system-tmc         policy-sync-extension-7c6b44b599-78vn9                        1/1     Running            0              4m42s
vmware-system-tmc         sync-agent-6bb458666c-xf95v                                   1/1     Running            0              9m2s
vmware-system-tmc         tmc-observer-7d79f484c4-dgqzg                                 1/1     Running            0              11m



kubectl get  httpproxy -A
NAMESPACE                 NAME                            FQDN                                     TLS SECRET                    STATUS   STATUS DESCRIPTION
baas-dev                  baas-httpproxy                  dev-baas.convergeict.com                 convergeict-2024-tls-secret   valid    Valid HTTPProxy
baas-dev                  enterprise-httpproxy            dev-enterprise.convergeict.com                                         valid    Valid HTTPProxy
baas-dev                  pay-httpproxy                   dev-pay.convergeict.com                  convergeict-2024-tls-secret   valid    Valid HTTPProxy
bidafiber-dev             bidafiber-web-httpproxy         dev-bidafiber.convergeict.com                                          valid    Valid HTTPProxy
enterprise                enterprise-httpproxy            dev-enterpriseportal.convergeict.com                                   valid    Valid HTTPProxy
gateway-dev               kong-gateway-httpproxy          dev-channels-gateway.convergeict.com                                   valid    Valid HTTPProxy
gateway-dev               konga-httpproxy                 dev-gatewayportal.convergeict.com        convergeict-tls-2024          valid    Valid HTTPProxy
iam-dev                   accounts-httpproxy              dev-iam-adapter.convergeict.com          convergeict-2024-tls-secret   valid    Valid HTTPProxy
iam-dev                   iam-bidafiber-httpproxy         dev-iam.bidafiber.ph                     bidafiber-2024                valid    Valid HTTPProxy
iam-dev                   keycloak-httpproxy              dev-iam.convergeict.com                  convergeict-2024-tls-secret   valid    Valid HTTPProxy
notification-dev          notification-portal-httpproxy   dev-notificationportal.convergeict.com                                 valid    Valid HTTPProxy
tanzu-system-dashboards   grafana-httpproxy               rel-devv-grafana.convergeict.com         grafana-tls                   valid    Valid HTTPProxy

```

To discover which versions of Kubernetes are made available by a management cluster, run the `tanzu kubernetes-release get command`:

```bash
tanzu kubernetes-release get

  NAME                       VERSION                  COMPATIBLE  ACTIVE  UPDATES AVAILABLE
  v1.22.17---vmware.1-tkg.2  v1.22.17+vmware.1-tkg.2  True        True
  v1.23.16---vmware.1-tkg.2  v1.23.16+vmware.1-tkg.2  True        True
  v1.24.10---vmware.1-tkg.2  v1.24.10+vmware.1-tkg.2  True        True
```

To discover the newer `tkr` versions to which you can upgrade a workload cluster running an older `tkr` version, run the t`anzu kubernetes-release available-upgrades`get command, specifying the current tkr version:

Command

```bash
tanzu cluster available-upgrades get {{ CLUSTER_NAME }}
```

```bash
tanzu cluster available-upgrades get tkg-dev-cluster
  NAME                       VERSION                  COMPATIBLE
  v1.23.16---vmware.1-tkg.2  v1.23.16+vmware.1-tkg.2  True
  v1.24.10---vmware.1-tkg.2  v1.24.10+vmware.1-tkg.2  True

```

This command lists all of the Kubernetes versions that are compatible with the specified cluster.

You cannot skip `minor` versions when upgrading your tkr version. For example, you cannot upgrade a cluster directly from `v1.22.x to v1.24.x`. You must upgrade a `v1.22.x` cluster to `v1.23.x` before upgrading the cluster to `v1.24.x`.

### Upgrade

Run the `tanzu cluster upgrade` command and enter y to confirm. To skip the confirmation step, specify the --yes option.
Command:

```bash
tanzu cluster upgrade {{ CLUSTER-NAME }} {{ TKR_VERSION }}
```

```bash
tanzu cluster upgrade tkg-dev-cluster --tkr v1.24.10---vmware.1-tkg.2 -v 6
Upgrading workload cluster 'tkg-dev-cluster' to kubernetes version 'v1.24.10+vmware.1', tkr version 'v1.24.10+vmware.1-tkg.2'. Are you sure? [y/N]: y
Warning: Use of ytt based cluster templates will be deprecated in favor of ClusterClass templates in a future version of TKG. Please work to move your workloads to a ClusterClass enabled cluster.
Verifying kubernetes version...
Retrieving configuration for upgrade cluster...
Create InfrastructureTemplate for upgrade...
Upgrading control plane nodes...
Patching KubeadmControlPlane with the kubernetes version v1.24.10+vmware.1...
Updating the KCP object with k8s version v1.24.10+vmware.1
Updating Audit APIVerison for KCP
Enabling Pod Security Standard for KCP
Waiting for kubernetes version to be updated for control plane nodes
Upgrading worker nodes...
Patching MachineDeployment with the kubernetes version v1.24.10+vmware.1...
Waiting for kubernetes version to be updated for worker nodes...

```

you can use this coomand to check the nodes are upgraded.

```bash
kubectl get nodes --context tkg-dev-cluster-admin@tkg-dev-cluster
NAME                                   STATUS   ROLES           AGE     VERSION
tkg-dev-cluster-control-plane-mzrhh    Ready    control-plane   10m     v1.24.10+vmware.1
tkg-dev-cluster-md-0-59c644fbc-z46bg   Ready    <none>          194d    v1.23.10+vmware.1
tkg-dev-cluster-md-0-7cc7769f9-m44r6   Ready    <none>          5m56s   v1.24.10+vmware.1
tkg-dev-cluster-md-0-7cc7769f9-skc79   Ready    <none>          84s     v1.24.10+vmware.1
tkg-dev-cluster-md-0-7cc7769f9-ztq76   Ready    <none>          3m30s   v1.24.10+vmware.1
```

Once the cluster updated successfully you will see output like

```bash
Waiting for packages to be up and running...
Cluster 'tkg-dev-cluster' successfully upgraded to kubernetes version 'v1.24.10+vmware.1'

```

You can check the cluster status from here.

```bash
tanzu cluster list
  NAME                 NAMESPACE  STATUS         CONTROLPLANE  WORKERS  KUBERNETES         ROLES   PLAN  TKR
  tkg-dev-cluster      default    running        1/1           4/4      v1.24.10+vmware.1  <none>  dev   v1.24.10---vmware.1-tkg.2
  tkg-preprod-cluster  default    running        3/3           3/3      v1.23.10+vmware.1  <none>  prod  v1.23.10---vmware.1-tkg.1
  tkg-staging-cluster  default    running        1/1           2/2      v1.23.10+vmware.1  <none>  dev   v1.23.10---vmware.1-tkg.1
  tkg-test-cluster     default    running        1/1           2/2      v1.23.10+vmware.1  <none>  dev   v1.23.10---vmware.1-tkg.1
  tkg-uat-cluster      default    createStalled  0/1           0/1      v1.21.2+vmware.1   <none>  dev   v1.21.2---vmware.1-tkg.1
```

```bash
 tkg-dev-cluster      default    running        1/1           4/4      v1.24.10+vmware.1  <none>  dev   v1.24.10---vmware.1-tkg.2
 ```

### List all the pods and Httpproxy in the dev cluster

```bash
kubectl get pods -A
NAMESPACE                 NAME                                                          READY   STATUS             RESTARTS       AGE
avi-system                ako-0                                                         1/1     Running            0              13m
baas-dev                  baas-integrations-56b64b94c4-hdbrb                            1/1     Running            0              9m2s
baas-dev                  baas-logs-checker-28453998-mrz9x                              0/1     Completed          0              44s
baas-dev                  backoffice-app-958bb8669-w2lll                                1/1     Running            0              4m42s
baas-dev                  boutil-app-b85f559-qdvvp                                      1/1     Running            0              7m3s
baas-dev                  cms-app-ff494598b-764xx                                       1/1     Running            0              9m2s
baas-dev                  ekyc-service-7d9c74bf9b-96ftw                                 0/1     ImagePullBackOff   0              4m42s
baas-dev                  ekyc-service-b9645c4fd-gg7c5                                  1/1     Running            0              11m
baas-dev                  enterprise-app-66594499f5-g5dpc                               1/1     Running            0              7m6s
baas-dev                  hobs-app-69855c7768-855zr                                     1/1     Running            0              4m41s
baas-dev                  lead-service-5656748967-sjx9b                                 1/1     Running            0              4m42s
baas-dev                  notifications-app-67ccf87d54-fmsjs                            1/1     Running            0              4m42s
baas-dev                  notifications-system-85c7d4dfc6-rwnln                         1/1     Running            0              11m
baas-dev                  openapi-app-5df5c88db8-z5h69                                  1/1     Running            0              7m6s
baas-dev                  payments-app-6487c4b78d-tlsv7                                 1/1     Running            0              11m
baas-dev                  sales-app-9bf9697f6-kcc68                                     1/1     Running            0              7m6s
baas-dev                  ticket-app-54f9cbb77-njswd                                    1/1     Running            0              7m6s
bidafiber-dev             bidafiber-web-744bcf575d-wxtpb                                1/1     Running            0              11m
bidafiber-dev             external-secrets-7f4c979df7-qvd77                             1/1     Running            0              9m2s
bidafiber-dev             external-secrets-cert-controller-dcf98465-tqfzn               1/1     Running            0              9m2s
bidafiber-dev             external-secrets-webhook-66b857759c-vp2tn                     1/1     Running            0              4m42s
cert-manager              cert-manager-6d857c9f47-hk8xm                                 1/1     Running            0              11m
cert-manager              cert-manager-cainjector-657c456698-l8pbw                      1/1     Running            0              11m
cert-manager              cert-manager-webhook-5f8d955b8f-wm2ms                         1/1     Running            0              7m6s
database                  mysql-5f85654d75-x9m7s                                        1/1     Running            0              11m
enterprise                enterprise-portal-deployment-79f7766c48-njvmh                 1/1     Running            0              11m
gateway-dev               backoffice-service-7bdc8d7d5b-xtt5s                           1/1     Running            0              9m2s
gateway-dev               boutility-service-7f44889fc5-d6dkh                            1/1     Running            0              8m59s
gateway-dev               cms-service-c6d7654b6-2ptsz                                   1/1     Running            0              11m
gateway-dev               enterprise-service-d467c8f78-mf2p6                            1/1     Running            0              9m2s
gateway-dev               hobs-service-6b7d9bf479-4jccv                                 1/1     Running            0              4m42s
gateway-dev               kong-566fd6bfc-54xk8                                          1/1     Running            0              9m2s
gateway-dev               konga-7c699df86d-qcpb9                                        1/1     Running            0              4m42s
gateway-dev               lead-service-746cb8b49c-mfwdg                                 1/1     Running            0              4m42s
gateway-dev               notification-service-848d64f547-bzvpz                         1/1     Running            0              4m42s
gateway-dev               notification-system-747d56f5c4-pzmt7                          1/1     Running            0              4m41s
gateway-dev               payment-service-594f5dd87d-dhxhz                              1/1     Running            0              4m41s
gateway-dev               sales-service-7d49bdc49c-7mv95                                1/1     Running            0              9m2s
gateway-dev               ticket-service-565c4ccffb-k47ht                               1/1     Running            0              9m1s
gofiber                   gofiber-web-66d55db4-vqfxb                                    1/1     Running            0              11m
iam-dev                   accounts-864f848676-8vxfl                                     1/1     Running            0              4m41s
iam-dev                   keycloak-7cc78c579f-8dc48                                     1/1     Running            0              7m6s
jupyter                   jupyter-74df795df9-ktqdf                                      1/1     Running            0              9m
kube-system               antrea-agent-54w2r                                            2/2     Running            0              9m33s
kube-system               antrea-agent-9fstj                                            2/2     Running            0              11m
kube-system               antrea-agent-g9lp8                                            2/2     Running            0              7m27s
kube-system               antrea-agent-nz8ll                                            2/2     Running            0              16m
kube-system               antrea-agent-rw79k                                            2/2     Running            0              5m13s
kube-system               antrea-controller-78b98d5b9d-88xr7                            1/1     Running            0              4m40s
kube-system               coredns-65fb98cc57-79z8q                                      1/1     Running            0              9m
kube-system               coredns-65fb98cc57-bdppp                                      1/1     Running            0              11m
kube-system               etcd-tkg-dev-cluster-control-plane-mzrhh                      1/1     Running            0              16m
kube-system               kube-apiserver-tkg-dev-cluster-control-plane-mzrhh            1/1     Running            0              16m
kube-system               kube-controller-manager-tkg-dev-cluster-control-plane-mzrhh   1/1     Running            0              15m
kube-system               kube-proxy-4kwdv                                              1/1     Running            0              11m
kube-system               kube-proxy-hsgws                                              1/1     Running            0              9m33s
kube-system               kube-proxy-m6k6d                                              1/1     Running            0              12m
kube-system               kube-proxy-nxqjc                                              1/1     Running            0              5m13s
kube-system               kube-proxy-w9gqj                                              1/1     Running            0              7m27s
kube-system               kube-scheduler-tkg-dev-cluster-control-plane-mzrhh            1/1     Running            0              16m
kube-system               kube-vip-tkg-dev-cluster-control-plane-mzrhh                  1/1     Running            0              15m
kube-system               metrics-server-8f4666b6b-f6tzd                                1/1     Running            0              11m
kube-system               vsphere-cloud-controller-manager-2x45b                        1/1     Running            0              16m
kube-system               vsphere-csi-controller-54b8fc57d4-b46wz                       7/7     Running            1 (13m ago)    14m
kube-system               vsphere-csi-node-8khwn                                        3/3     Running            0              7m27s
kube-system               vsphere-csi-node-d6lzm                                        3/3     Running            0              11m
kube-system               vsphere-csi-node-qblpd                                        3/3     Running            1 (15m ago)    16m
kube-system               vsphere-csi-node-vtqnr                                        3/3     Running            0              5m13s
kube-system               vsphere-csi-node-wqpw7                                        3/3     Running            0              9m33s
miniprojects              cxp-api-79fb54cbb4-dqwkc                                      1/1     Running            0              4m41s
miniprojects              todo-api-748b7b5694-kk4hx                                     0/1     Error              6 (3m ago)     9m
notification-dev          notification-portal-deployment-64ffb9c879-4rwq9               1/1     Running            0              4m40s
olm                       catalog-operator-69f7ffbc74-c865g                             1/1     Running            0              11m
olm                       olm-operator-d6d858764-fth9m                                  1/1     Running            0              11m
olm                       operatorhubio-catalog-k5zqw                                   1/1     Running            0              7m2s
olm                       packageserver-68b6c4c69d-7tkn2                                1/1     Running            0              11m
olm                       packageserver-68b6c4c69d-ww5v6                                1/1     Running            0              7m6s
pinniped-concierge        pinniped-concierge-6d8f4848b6-bwwct                           1/1     Running            0              11m
pinniped-concierge        pinniped-concierge-6d8f4848b6-fbv89                           1/1     Running            0              7m6s
pinniped-concierge        pinniped-concierge-kube-cert-agent-b65ccd5d-f5snc             1/1     Running            0              14m
prereg-dev                cpe-api-675749fddb-h54h9                                      1/1     Running            0              8m59s
prereg-dev                cpe-api-675749fddb-s9cnf                                      1/1     Running            0              4m40s
prereg-dev                preregistration-deployment-59cd58554-4lqmp                    0/1     CrashLoopBackOff   6 (55s ago)    9m2s
pwm                       pwm-7444567649-vmf4r                                          1/1     Running            0              7m5s
rabbitmq                  rabbitmq-6dc49c775f-2lb8q                                     1/1     Running            0              9m1s
redis-dev                 redis-master-0                                                1/1     Running            0              4m27s
redis-dev                 redis-replicas-0                                              1/1     Running            4 (2m3s ago)   8m51s
redis-dev                 redis-replicas-1                                              1/1     Running            0              4m30s
redis-dev                 redis-replicas-2                                              1/1     Running            4 (114s ago)   5m58s
tanzu-system-dashboards   grafana-74b8b59f94-4z67p                                      2/2     Running            0              11m
tanzu-system-ingress      contour-7455c94764-66bkv                                      1/1     Running            0              11m
tanzu-system-ingress      contour-7455c94764-75dcm                                      1/1     Running            0              7m4s
tanzu-system-ingress      envoy-4xq28                                                   2/2     Running            0              7m6s
tanzu-system-ingress      envoy-794kd                                                   2/2     Running            0              11m
tanzu-system-ingress      envoy-stn42                                                   2/2     Running            0              4m42s
tanzu-system-ingress      envoy-xgzn4                                                   2/2     Running            0              9m2s
tanzu-system-logging      fluent-bit-9rws9                                              1/1     Running            0              16m
tanzu-system-logging      fluent-bit-bjz6p                                              1/1     Running            0              7m27s
tanzu-system-logging      fluent-bit-gqwd5                                              1/1     Running            0              11m
tanzu-system-logging      fluent-bit-h686m                                              1/1     Running            0              9m33s
tanzu-system-logging      fluent-bit-pflnr                                              1/1     Running            0              5m13s
tanzu-system-monitoring   alertmanager-6958d95df-s9gxk                                  1/1     Running            0              11m
tanzu-system-monitoring   prometheus-kube-state-metrics-6459db4f56-frbxt                1/1     Running            0              11m
tanzu-system-monitoring   prometheus-node-exporter-6l6wz                                1/1     Running            0              11m
tanzu-system-monitoring   prometheus-node-exporter-88xld                                1/1     Running            0              16m
tanzu-system-monitoring   prometheus-node-exporter-hbswr                                1/1     Running            0              9m33s
tanzu-system-monitoring   prometheus-node-exporter-sm9dc                                1/1     Running            0              5m13s
tanzu-system-monitoring   prometheus-node-exporter-x9ctm                                1/1     Running            0              7m27s
tanzu-system-monitoring   prometheus-pushgateway-5d9cd8c5c-b45zl                        1/1     Running            0              4m39s
tanzu-system-monitoring   prometheus-server-665987f75b-jndxs                            2/2     Running            0              7m5s
tkg-system                kapp-controller-5fb55c6b69-6pn6p                              2/2     Running            0              9m1s
tkg-system                tanzu-capabilities-controller-manager-6ff97656b8-5tswq        1/1     Running            0              11m
tracing                   springboot-api-d4c7d5f89-76d6c                                1/1     Running            0              7m5s
tracing                   zipkin-857b9bb6fc-lphmm                                       1/1     Running            0              11m
velero                    node-agent-672qk                                              1/1     Running            0              9m2s
velero                    node-agent-7wcl4                                              1/1     Running            0              15m
velero                    node-agent-bxkch                                              1/1     Running            0              11m
velero                    node-agent-d8f72                                              1/1     Running            0              7m6s
velero                    node-agent-m8f7n                                              1/1     Running            0              4m42s
velero                    velero-86b7c54b6b-6ctcx                                       1/1     Running            0              7m4s
vmware-system-tmc         agent-updater-f6487d865-cmtzz                                 1/1     Running            0              7m4s
vmware-system-tmc         agentupdater-workload-28453998-glz2x                          0/1     Completed          0              59s
vmware-system-tmc         cluster-auth-pinniped-5cd96898b9-5wm9g                        1/1     Running            0              11m
vmware-system-tmc         cluster-auth-pinniped-5cd96898b9-d6zpw                        1/1     Running            0              7m5s
vmware-system-tmc         cluster-auth-pinniped-kube-cert-agent-68679d5687-pdk6p        1/1     Running            0              12m
vmware-system-tmc         cluster-health-extension-56b94bdbdd-76cqc                     1/1     Running            0              11m
vmware-system-tmc         cluster-secret-78cc799d5f-74ncq                               1/1     Running            0              4m39s
vmware-system-tmc         data-protection-6fc85c8c7-w7hxc                               1/1     Running            0              7m4s
vmware-system-tmc         extension-manager-69dc6995d7-2vkll                            1/1     Running            0              7m4s
vmware-system-tmc         extension-updater-9d6c6bc6c-jggzp                             1/1     Running            0              4m40s
vmware-system-tmc         gatekeeper-operator-manager-746d8fb49f-wbp8h                  1/1     Running            0              7m3s
vmware-system-tmc         inspection-extension-679679456f-wbfh9                         1/1     Running            0              9m1s
vmware-system-tmc         intent-agent-76d847976f-qwvhr                                 1/1     Running            0              4m39s
vmware-system-tmc         package-deployment-f4d976677-9rthb                            1/1     Running            0              7m5s
vmware-system-tmc         policy-insight-extension-manager-9d8f886c8-gk8zs              1/1     Running            0              9m
vmware-system-tmc         policy-sync-extension-7c6b44b599-78vn9                        1/1     Running            0              4m42s
vmware-system-tmc         sync-agent-6bb458666c-xf95v                                   1/1     Running            0              9m2s
vmware-system-tmc         tmc-observer-7d79f484c4-dgqzg                                 1/1     Running            0              11m



kubectl get  httpproxy -A
NAMESPACE                 NAME                            FQDN                                     TLS SECRET                    STATUS   STATUS DESCRIPTION
baas-dev                  baas-httpproxy                  dev-baas.convergeict.com                 convergeict-2024-tls-secret   valid    Valid HTTPProxy
baas-dev                  enterprise-httpproxy            dev-enterprise.convergeict.com                                         valid    Valid HTTPProxy
baas-dev                  pay-httpproxy                   dev-pay.convergeict.com                  convergeict-2024-tls-secret   valid    Valid HTTPProxy
bidafiber-dev             bidafiber-web-httpproxy         dev-bidafiber.convergeict.com                                          valid    Valid HTTPProxy
enterprise                enterprise-httpproxy            dev-enterpriseportal.convergeict.com                                   valid    Valid HTTPProxy
gateway-dev               kong-gateway-httpproxy          dev-channels-gateway.convergeict.com                                   valid    Valid HTTPProxy
gateway-dev               konga-httpproxy                 dev-gatewayportal.convergeict.com        convergeict-tls-2024          valid    Valid HTTPProxy
iam-dev                   accounts-httpproxy              dev-iam-adapter.convergeict.com          convergeict-2024-tls-secret   valid    Valid HTTPProxy
iam-dev                   iam-bidafiber-httpproxy         dev-iam.bidafiber.ph                     bidafiber-2024                valid    Valid HTTPProxy
iam-dev                   keycloak-httpproxy              dev-iam.convergeict.com                  convergeict-2024-tls-secret   valid    Valid HTTPProxy
notification-dev          notification-portal-httpproxy   dev-notificationportal.convergeict.com                                 valid    Valid HTTPProxy
tanzu-system-dashboards   grafana-httpproxy               rel-devv-grafana.convergeict.com         grafana-tls                   valid    Valid HTTPProxy

```

### How to Delete a workload cluster

To Delete a workload cluster Login to the management cluster where you have workload cluster run the following command

```bash
tanzu login
? Select a server nprod-tkg-management-cluster()
✔  successfully logged in to management cluster using the kubeconfig nprod-tkg-management-cluster
Checking for required plugins...
All required plugins are already installed and up-to-date
```

**Command:**

```bash
tanzu cluster delete {{ CLUSTER_NAME }}
```

```bash

 tanzu cluster delete tkg-uat-cluster
Deleting workload cluster 'tkg-uat-cluster'. Are you sure? [y/N]: y
Workload cluster 'tkg-uat-cluster' is being deleted

tanzu cluster list
  NAME                 NAMESPACE  STATUS    CONTROLPLANE  WORKERS  KUBERNETES         ROLES   PLAN  TKR
  tkg-dev-cluster      default    running   1/1           4/4      v1.24.10+vmware.1  <none>  dev   v1.24.10---vmware.1-tkg.2
  tkg-preprod-cluster  default    running   3/3           3/3      v1.23.10+vmware.1  <none>  prod  v1.23.10---vmware.1-tkg.1
  tkg-staging-cluster  default    running   1/1           2/2      v1.23.10+vmware.1  <none>  dev   v1.23.10---vmware.1-tkg.1
  tkg-test-cluster     default    running   1/1           2/2      v1.23.10+vmware.1  <none>  dev   v1.23.10---vmware.1-tkg.1
  tkg-uat-cluster      default    deleting  0/1                    v1.21.2+vmware.1   <none>  dev   v1.21.2---vmware.1-tkg.1
```
