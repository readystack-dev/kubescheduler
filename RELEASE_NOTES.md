# kube-scheduler v1.36.6 — ReadyStack release `v1.36.6-CE-ubuntu24.04-r5`

Generated 2026-11-21 from this release's build records and strategy. Statements that come from a single record name it in brackets; the others come from this release's strategy and build log.

## Release

- **Image:** `readystack/kubescheduler:v1.36.6-CE-ubuntu24.04-r5` (linux/amd64). Index digest `sha256:f332a5ce81b6f8b8d75638f8b7a1c18132a7ddb678dae2950a170b8212594ce6` [`docs/provenance/LOCKED_DIGEST.txt`].
- **Image manifest digest:** `sha256:96cc95e028f81d33577a396cf44b73530017a25dd5abedb2fb3d9036fcdb04f3` [`TARGET_DIGEST.txt`].
- **Upstream:** kube-scheduler v1.36.6 built from source at `v1.36.6` of https://github.com/kubernetes/kubernetes — upstream notes: https://github.com/kubernetes/kubernetes/releases/tag/v1.36.6 [`docs/provenance/BUILD_RECIPE.json`].
- **Base image:** `ubuntu:24.04`; runtime user `kube`.
- **Build:** `source-rebuild-go`, compiled offline with `--network=none`.
- **Supersedes:** ReadyStack release `v1.36.6-CE-ubuntu24.04-r4`.

## What changed in this release

- Upstream version unchanged (v1.36.6); ReadyStack rebuild.
- Minimum dependency versions ReadyStack enforces on top of upstream (raised only where upstream's own version was lower); shipped versions from the SBOM [`docs/sbom/SBOM.json`]:
  - `golang.org/x/net` raised from upstream's `v0.56.0` to at least `v0.62.0` (shipped: `v0.62.0`)
  - `golang.org/x/crypto` raised from upstream's `v0.53.0` to at least `v0.56.0` (shipped: `v0.58.0`)
  - `golang.org/x/text` raised from upstream's `v0.39.0` to at least `v0.41.0` (shipped: `v0.43.0`)
  - `google.golang.org/grpc` raised from upstream's `v1.79.3` to at least `v1.83.2` (shipped: `v1.83.2`)
  - `go.opentelemetry.io/otel/sdk` raised from upstream's `v1.40.0` to at least `v1.44.0` (shipped: `v1.44.0`)
  - `go.opentelemetry.io/otel` raised from upstream's `v1.41.0` to at least `v1.44.0` (shipped: `v1.44.0`)
  - `go.opentelemetry.io/otel/metric` raised from upstream's `v1.41.0` to at least `v1.44.0` (shipped: `v1.44.0`)
  - `go.opentelemetry.io/otel/trace` raised from upstream's `v1.41.0` to at least `v1.44.0` (shipped: `v1.44.0`)
  - `go.opentelemetry.io/otel/exporters/otlp/otlptrace` raised from upstream's `v1.40.0` to at least `v1.44.0` (shipped: `v1.44.0`)
  - `go.opentelemetry.io/otel/exporters/otlp/otlptrace/otlptracegrpc` raised from upstream's `v1.40.0` to at least `v1.44.0` (shipped: `v1.44.0`)
  - `go.etcd.io/etcd/client/pkg/v3` raised from upstream's `v3.6.8` to at least `v3.6.16` (shipped: `v3.6.16`)
  - `go.etcd.io/etcd/client/v3` raised from upstream's `v3.6.8` to at least `v3.6.16` (shipped: `v3.6.16`)
  - `go.etcd.io/etcd/api/v3` raised from upstream's `v3.6.8` to at least `v3.6.16` (shipped: `v3.6.16`)
- kube-scheduler v1.36.6 is unchanged from the release this supersedes (v1.36.6-CE-ubuntu24.04-r4); this release rebuilds it. Settings, ports and data volumes are those of v1.36.6-CE-ubuntu24.04-r4: replace the container with this image on the same volumes.

## Security status at release (measured 2026-11-21)

- **Docker Scout:** 0 fixable critical/high vulnerabilities; runs as `999:999` (non-root); SBOM + provenance attestations present (SPDX SBOM + SLSA v1) [`docs/provenance/SCOUT_VERDICT.json`].
- **OSV cross-check:** no fixable critical/high vulnerability beyond Docker Scout's findings.
- **Signature:** cosign signature verified by ReadyStack's release gate before publishing; check it yourself with `cosign verify --key https://readystack.dev/keys/cosign.pub readystack/kubescheduler:v1.36.6-CE-ubuntu24.04-r5` (signatures are recorded in the public Sigstore transparency log).
- **ReadyStack release check at release:** passed (ReadyStack's own test, not a Docker Scout grade). Vulnerabilities are disclosed every day and the image never changes, so it is re-checked daily, beside Docker Scout's own dated grade, at https://readystack.dev/queue.
- **Before exposing this service:** The image's default user is the unprivileged kube (999:999). Under kubeadm the static Pod runs it as root through the kube-scheduler patch file in the Kubernetes VM Stack install bundle (`controlplane/patches/kube-scheduler+strategic.yaml`): the component reads /etc/kubernetes/scheduler.conf, which kubeadm writes mode 0600 root and which holds its client credential for the API server, so treat the node's /etc/kubernetes like the keys themselves.

## Reproducibility

Rebuilds offline from this archive to the identical image digest `sha256:96cc95e028f81d33577a396cf44b73530017a25dd5abedb2fb3d9036fcdb04f3`: run `./BUILD_OFFLINE.sh`, then `./VERIFY_OFFLINE.sh` (add `--online` to confirm the Docker Hub tag holds the same digest). Before publishing, ReadyStack rebuilt the kit with `./BUILD_OFFLINE.sh` and compared the result with the published digest [`docs/provenance/REPRO_VERIFICATION.json`], then ran `./BUILD_OFFLINE.sh` and `./VERIFY_OFFLINE.sh` from this archive (without `--online`).

## Validation

- 12 of 12 runbook checks passed (12 authored) on the stack's 3-node integration cluster, recorded 2026-10-29T02:34:55Z [`docs/validation/test-results.json`, from the run's record `docs/stack/RUNBOOK_EVIDENCE.json`]. They are the checks of the stack's operator runbook that name this component; no container of this image was started on its own for them. The record does not show this release's image (`sha256:96cc95e028f81d33577a396cf44b73530017a25dd5abedb2fb3d9036fcdb04f3`) in that run: it lists the component's images as they were then, an earlier build or a tag, so the checks describe the component, not this digest.
- Failed cases are listed in `docs/validation/FAILED_TESTS.md`.

## Known issues

- None found (reviewed by held semantic review (Claude) on 2026-11-21).

## Defects fixed

- 1 problem found before release and fixed before it shipped (1 by review); each is listed in `docs/defects/defects.json`.

## Verify this archive

```
sha256sum -c CHECKSUMS.sha256
./BUILD_OFFLINE.sh && ./VERIFY_OFFLINE.sh --online
cosign verify --key https://readystack.dev/keys/cosign.pub readystack/kubescheduler:v1.36.6-CE-ubuntu24.04-r5
```

ReadyStack records every signature in the public Sigstore transparency log (Rekor), so the command needs no extra flags.

