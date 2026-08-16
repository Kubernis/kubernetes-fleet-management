# fleet-management

The platform team's GitOps control plane for cluster bootstrap. Desired state
for the clusters we own lives here; ArgoCD reconciles it. ArgoCD, the Port
Kubernetes exporter and the Port ArgoCD integration are adopted, not managed.

## Bootstrap

    kubectl apply -f bootstrap/root-app.yaml

The only manual step. It is an app-of-apps: it syncs `clusters/local-k3d/` flat
and each Application there points at its sibling `manifests/` subdirectory.
Sync waves order them (namespaces 0, policies 1) — they share one parent.

## Tenant environments

Developers do not write manifests here. They request an environment and Port
writes `tenants/<name>/tenant.yaml` — four scalars, nothing else:

    tenant:
      name: checkout-dev
      team: checkout
      stage: dev
      tier: small

The `tenants` ApplicationSet (`clusters/local-k3d/tenants-appset.yaml`) watches
`tenants/*/tenant.yaml` and renders `charts/tenant` for each one: namespace,
resource quota, default container limits, deny-by-default ingress.

The chart is the standard. Change a tier's numbers there and every tenant on
that tier converges at the next sync. Deleting a `tenant.yaml` removes the
namespace and everything in it.

## Adding a spoke cluster

Add `clusters/<name>/` with the same layout — Applications at the top,
manifests underneath — plus one root Application pointing at it. The folder
name is the cluster name, which keeps this to a single new file.

## Port

Port discovers this repo via `port.yml` (service, owned by `platform_team`).
