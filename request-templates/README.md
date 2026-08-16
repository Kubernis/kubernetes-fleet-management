# request-templates

Templates the self-service dispatcher renders. Platform-team owned.

`tenant.yaml.tmpl` is filled by `.github/workflows/request-tenant.yaml` using
`envsubst` and written to `tenants/<team>-<stage>/tenant.yaml`.

It lives here rather than under `tenants/` because the ApplicationSet globs
`tenants/*/tenant.yaml` — a template parked in there would be generated as a
real Application named after its own folder.

Adding a field means changing three things together: this template, the Port
form that supplies the value, and `charts/tenant` that consumes it. That
coupling is deliberate — it is what stops the form and the repository drifting
apart.
