# Provenance — kube-scheduler (`v1.36.6-CE-ubuntu24.04-r5`)

Every item below is copied from a record in this archive; the record is named in brackets.

## Source

- Upstream repository: https://github.com/kubernetes/kubernetes
- Revision built: `v1.36.6` (commit `1b2171b110bb2a2542e1322035c6583ec86eb976`)
- Source tree: `803692e6a7661d2d72c4cdf0a2a2459bdf6646e2b6c955ed7362f279ee4fa004` over 29206 files [`docs/provenance/SOURCE_TREE_HASH.json`]
- Community Edition only: enterprise code is removed or refused before the build (CE audit at acquire).

## Build

- Method: `source-rebuild-go`; base image `ubuntu:24.04`
- Compiled with `--network=none` from vendored source, dependencies and base images; BuildKit pinned to `moby/buildkit:v0.31.2` (shipped in `toolchain/`).
- SOURCE_DATE_EPOCH: `1795244555` [`SOURCE_DATE_EPOCH.txt`]
- Frozen inputs: [`docs/provenance/FREEZE_MANIFEST.json`], build recipe: [`docs/provenance/BUILD_RECIPE.json`].

## Output

- Published image: `readystack/kubescheduler:v1.36.6-CE-ubuntu24.04-r5` — index digest `sha256:f332a5ce81b6f8b8d75638f8b7a1c18132a7ddb678dae2950a170b8212594ce6` [`docs/provenance/LOCKED_DIGEST.txt`]
- linux/amd64 image digest `sha256:96cc95e028f81d33577a396cf44b73530017a25dd5abedb2fb3d9036fcdb04f3` — reproduced from this archive before publishing [`docs/provenance/REPRO_VERIFICATION.json`].

## Attestations and signature

- SBOM + SLSA provenance attached to the image: attached: an SPDX SBOM and SLSA v1 provenance [`docs/provenance/SCOUT_VERDICT.json`]
- cosign signature by the ReadyStack release key: verified before publishing. Check it: `cosign verify --key https://readystack.dev/keys/cosign.pub readystack/kubescheduler:v1.36.6-CE-ubuntu24.04-r5`

## Pinned inputs, and why

- 32 pinned inputs, each with where it came from, why this one, how it was checked and when it was decided: `docs/PINS.md` [`docs/provenance/PINS.json`] (upstream source 2, base and build images 3, image digests 2, dependency versions readystack sets 16, components from other repositories 1, pins in the kit's dockerfiles 3, readystack's build engine 1, dependencies upstream chose 2, services the release checks run beside 2).

## Build trail

- How this release passed through the ReadyStack harness, record by record (every chain, drive, stage, review round, defect and check, each naming the record it is copied from): `ReadyStack-kubescheduler-v1.36.6-CE-ubuntu24.04-r5.trail.json`, published beside this archive and signed with the release key (`ReadyStack-kubescheduler-v1.36.6-CE-ubuntu24.04-r5.trail.json.sigstore`), and readable at https://readystack.dev/products/kubescheduler/trail/v1.36.6-CE-ubuntu24.04-r5.

