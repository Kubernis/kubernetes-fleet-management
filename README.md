# fleet-management

The platform team's GitOps control plane for cluster bootstrap. Desired state
for the clusters we own lives here; ArgoCD reconciles it. ArgoCD, the Port
Kubernetes exporter and the Port ArgoCD integration are adopted, not managed.

## Bootstrap

    kubectl apply -f bootstrap/root-app.yaml

The only manual step. It is an app-of-apps: it syncs `clusters/local-k3d/` flat
and each Application there points at its sibling `manifests/` subdirectory.
Sync waves order them (namespaces 0, policies 1) — they share one parent.

## Adding a spoke cluster

Add `clusters/<name>/` with the same layout — Applications at the top,
manifests underneath — plus one root Application pointing at it. The folder
name is the cluster name, which keeps this to a single new file. At fleet scale
this becomes an ApplicationSet with a git or clusters generator over
`clusters/*`; the layout already suits it.

## Port

Port discovers this repo via `port.yml` (service, owned by `platform_team`).
