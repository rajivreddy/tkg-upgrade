# Update Contour service with Static IP address

Get contour default values

```bash
kubectl get secret contour-tkg-package-values  -n tkg-package -o yaml
apiVersion: v1
data:
  contour.yaml: LS0tCmluZnJhc3RydWN0dXJlX3Byb3ZpZGVyOiB2c3BoZXJlCm5hbWVzcGFjZTogdGFuenUtc3lzdGVtLWluZ3Jlc3MKY29udG91cjoKIGNvbmZpZ0ZpbGVDb250ZW50czoge30KIHVzZVByb3h5UHJvdG9jb2w6IGZhbHNlCiByZXBsaWNhczogMgogcHNwTmFtZXM6ICJ2bXdhcmUtc3lzdGVtLXJlc3RyaWN0ZWQiCiBsb2dMZXZlbDogaW5mbwplbnZveToKIHNlcnZpY2U6CiAgIHR5cGU6IExvYWRCYWxhbmNlcgogICBsb2FkQmFsYW5jZXJJUDogMTcyLjMxLjkzLjEyCiAgIGFubm90YXRpb25zOiB7fQogICBleHRlcm5hbFRyYWZmaWNQb2xpY3k6IENsdXN0ZXIKICAgZGlzYWJsZVdhaXQ6IGZhbHNlCiBob3N0UG9ydHM6CiAgIGVuYWJsZTogdHJ1ZQogICBodHRwOiA4MAogICBodHRwczogNDQzCiBob3N0TmV0d29yazogZmFsc2UKIHRlcm1pbmF0aW9uR3JhY2VQZXJpb2RTZWNvbmRzOiAzMDAKIGxvZ0xldmVsOiBpbmZvCmNlcnRpZmljYXRlczoKIGR1cmF0aW9uOiA4NzYwaAogcmVuZXdCZWZvcmU6IDM2MGg=
kind: Secret
metadata:
  creationTimestamp: "2021-11-19T04:07:21Z"
  name: contour-tkg-package-values
  namespace: tkg-package
  resourceVersion: "626479096"
  uid: 0f3c66ca-a37c-4233-96b8-b5a310d3624e
type: Opaque
```


```bash
echo "LS0tCmluZnJhc3RydWN0dXJlX3Byb3ZpZGVyOiB2c3BoZXJlCm5hbWVzcGFjZTogdGFuenUtc3lzdGVtLWluZ3Jlc3MKY29udG91cjoKIGNvbmZpZ0ZpbGVDb250ZW50czoge30KIHVzZVByb3h5UHJvdG9jb2w6IGZhbHNlCiByZXBsaWNhczogMgogcHNwTmFtZXM6ICJ2bXdhcmUtc3lzdGVtLXJlc3RyaWN0ZWQiCiBsb2dMZXZlbDogaW5mbwplbnZveToKIHNlcnZpY2U6CiAgIHR5cGU6IExvYWRCYWxhbmNlcgogICBsb2FkQmFsYW5jZXJJUDogMTcyLjMxLjkzLjEyCiAgIGFubm90YXRpb25zOiB7fQogICBleHRlcm5hbFRyYWZmaWNQb2xpY3k6IENsdXN0ZXIKICAgZGlzYWJsZVdhaXQ6IGZhbHNlCiBob3N0UG9ydHM6CiAgIGVuYWJsZTogdHJ1ZQogICBodHRwOiA4MAogICBodHRwczogNDQzCiBob3N0TmV0d29yazogZmFsc2UKIHRlcm1pbmF0aW9uR3JhY2VQZXJpb2RTZWNvbmRzOiAzMDAKIGxvZ0xldmVsOiBpbmZvCmNlcnRpZmljYXRlczoKIGR1cmF0aW9uOiA4NzYwaAogcmVuZXdCZWZvcmU6IDM2MGg=" | base64 -d > contour-config.yaml

cat contour-config.yaml
---
infrastructure_provider: vsphere
namespace: tanzu-system-ingress
contour:
 configFileContents: {}
 useProxyProtocol: false
 replicas: 2
 pspNames: "vmware-system-restricted"
 logLevel: info
envoy:
 service:
   type: LoadBalancer
   annotations: {}
   externalTrafficPolicy: Cluster
   disableWait: false
 hostPorts:
   enable: true
   http: 80
   https: 443
 hostNetwork: false
 terminationGracePeriodSeconds: 300
 logLevel: info
certificates:
 duration: 8760h
```

add envoy.service.loadBalancerIP to contour-config.yaml with right IP address save the file


generate base64 encode for the contour-config.yaml file

```bash
cat contour-config.yaml | base64
```

edit the contour-tkg-package-values secret and update the contour.yaml with the new base64 encoded value

```bash   
kubectl edit secret contour-tkg-package-values -n tkg-package
```

force recosile the contour package

```bash
kubectl patch pkgi -n tkg-package contour -p '{"spec":{"paused":true}}' --type=merge
kubectl patch pkgi -n tkg-package contour -p '{"spec":{"paused":false}}' --type=merge
```
