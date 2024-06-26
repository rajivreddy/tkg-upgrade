# Install the Tanzu CLI Plugins for TKG v2.3

List the standalone Tanzu CLI plugins for Tanzu Kubernetes Grid v2.3

```bash

tanzu plugin group get vmware-tkg/default:v2.3.1

Plugins in Group:  vmware-tkg/tkg:v2.3.1
NAME                TARGET      VERSION
isolated-cluster    global      v0.30.2
management-cluster  kubernetes  v0.30.2
package             kubernetes  v0.30.2
pinniped-auth       global      v0.30.2
secret              kubernetes  v0.30.2
telemetry           kubernetes  v0.30.2
```

To list all available versions for the vmware-tkg/default plugin group, run

```bash
tanzu plugin group search -n vmware-tkg/default --show-details
```

Install the standalone plugins for Tanzu Kubernetes Grid v2.3.x

```bash
tanzu plugin install --group vmware-tkg/default:v2.3.1
```

Verify that the plugins installed successfully

```bash
tanzu plugin list
```

**Note:**

Each plugin name and plugin version returned by `tanzu plugin group get vmware-tkg/default:v2.3.1` must be included in the output of `tanzu plugin list`.
