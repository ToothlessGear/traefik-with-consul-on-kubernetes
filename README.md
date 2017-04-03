Four releases. Each one includes just 1 chart.

- namespace
- consul cluster
- traefik with consul kv store backend
- client backup release. It is a pod that constantly is backing up the certificates

Sensitive data:

- Substitute the 

``
  defaultCert: XXXXXXXXXXXX
  defaultKey: YYYYYYYYYYY 
``

with your proper base64 values

You cannot delete namespace networking via helm. This is a persistent volume delete protection. If the
namespace is deleted via helm, so the PVs. And the client-backup deployment save the backup of the certificates.
( ..looking for s3 backup rather than GcePersistentDisk backup )
If you want to remove the namespace, you should use kubectl regular delete namespace command.

* $ ``helm install namespace --name namespace-release``
* $ ``helm install traefik-consul --name traefik-consul-release``
* $ ``helm install --set staticIP=XX.XX.XX.XX traefik --name traefik-release``
* $ ``helm install client-backup  --name client-backup-release``
