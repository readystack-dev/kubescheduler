# kube-scheduler: ReadyStack build kit

The offline build kit for the Docker image `readystack/kubescheduler:v1.36.6-CE-ubuntu24.04-r5`. It holds what the image is built from (upstream source, base images, toolchain, pinned dependencies) and the documents, so the image can be rebuilt with no network access (`docker build --network=none`).

- Release: [v1.36.6-CE-ubuntu24.04-r5](https://github.com/readystack-dev/kubescheduler/releases/tag/v1.36.6-CE-ubuntu24.04-r5)
- Image: [`docker.io/readystack/kubescheduler:v1.36.6-CE-ubuntu24.04-r5`](https://hub.docker.com/r/readystack/kubescheduler/tags), manifest digest `sha256:96cc95e028f81d33577a396cf44b73530017a25dd5abedb2fb3d9036fcdb04f3`
- Product page: https://readystack.dev/products/kubescheduler
- Defects: this repository's Issues (defects recorded from 25 November 2026 on) and https://readystack.dev/defects.html?product=kubescheduler

kube-scheduler is distributed under its upstream licence (Apache-2.0); the kit contains upstream's source under that licence, and each vendored component under its own.

## Download

From the [v1.36.6-CE-ubuntu24.04-r5 release](https://github.com/readystack-dev/kubescheduler/releases/tag/v1.36.6-CE-ubuntu24.04-r5): `ReadyStack-kubescheduler-v1.36.6-CE-ubuntu24.04-r5.tar.gz` (501.3 MB), SHA-256 `47c36f784c6a5afdd3ed51e577d5ca9f862f963c2b818187c220fa72a5517fb1`.

```sh
sha256sum --check --ignore-missing SHA256SUMS
```

## Verify and rebuild

The rebuild statement is signed with the ReadyStack release key. Check the signature before you rely on it:

```sh
cosign verify-blob --key https://readystack.dev/keys/cosign.pub \
  --bundle ReadyStack-kubescheduler-v1.36.6-CE-ubuntu24.04-r5.rebuild.json.sigstore ReadyStack-kubescheduler-v1.36.6-CE-ubuntu24.04-r5.rebuild.json
```

Unpack with `-p` so file modes are kept, then follow the kit's own README.md and docs/REBUILD.md:

```sh
tar -xpzf ReadyStack-kubescheduler-v1.36.6-CE-ubuntu24.04-r5.tar.gz
cd v1.36.6-CE-ubuntu24.04-r5
less README.md docs/REBUILD.md
```

The build trail (`ReadyStack-kubescheduler-v1.36.6-CE-ubuntu24.04-r5.trail.json`, signed the same way, and its Markdown copy) records how this release was built and checked.

## This repository

`recipe/` holds the kit's recipe files (Dockerfiles, build scripts, pins) as published at https://readystack.dev/products/kubescheduler/recipe. The complete kit is the release download. Each release is a tag here.
