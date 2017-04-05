Four releases. Each one includes just 1 chart.

- namespace
- consul cluster
- traefik with consul kv store backend ( stable version )
- client backup release. It is a pod that constantly is backing up the certificates. If the cluster of consul needs to be recreated
again, it would be helpful to use a backup of acme certificates. 

Sensitive data:

- Substitute the 

```yaml
  defaultCert: XXXXXXXXXXXX
  defaultKey: YYYYYYYYYYY 
```

in Secret traefik release resource, with your proper base64 values

You cannot delete namespace networking via helm. This is a persistent volume delete protection. If the
namespace is deleted via helm, so the PVs. And the client-backup deployment save the backup of the certificates.
( ..looking for s3 backup rather than GcePersistentDisk backup )
If you want to remove the namespace, you should use kubectl regular delete namespace command.

``$ helm install namespace --name namespace-release``
``$ helm install traefik-consul --name traefik-consul-release``
``$ helm install --name traefik-release --values values.yaml stable/traefik``
``$ helm install client-backup  --name client-backup-release``

Values.yaml file for traefik-release would be


```yaml
# Default values for Traefik
image: traefik
imageTag: v1.2.1-alpine
serviceType: LoadBalancer
replicas: 1
cpuRequest: 100m
memoryRequest: 20Mi
cpuLimit: 100m
memoryLimit: 30Mi
configfile: /config/traefik.toml
ssl:
  enabled: true
  enforced: false
  defaultCert: XXXXXXXXXXXX
  defaultKey: YYYYYYYYYYY
acme:
  enabled: true
  email: admin@example.com
  staging: true
  onDemand: true
  persistence:
    enabled: false
    ## If defined, volume.beta.kubernetes.io/storage-class: <storageClass>
    ## Default: volume.alpha.kubernetes.io/storage-class: default
    ##
    # storageClass:
    accessMode: ReadWriteOnce
    size: 1Gi
dashboard:
  enabled: false
  domain: traefik.example.com
  ingress:
  auth:
service:
  # annotations:
  #   key: value
  # labels:
  #   key: value
consul:
  enabled: true
  endpoint: "traefik-consul:8500"
  watch: true
  prefix: traefik
```
