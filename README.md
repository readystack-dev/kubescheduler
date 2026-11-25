# kube-scheduler: ReadyStack build kit

The offline build kit for the Docker image `readystack/kubescheduler:v1.36.6-CE-ubuntu24.04-r5`. It holds what the image is built from (upstream source, base images, toolchain, pinned dependencies) and the documents, so the image can be rebuilt with no network access (`docker build --network=none`).

- Release: [v1.36.6-CE-ubuntu24.04-r5](https://github.com/readystack-dev/kubescheduler/releases/tag/v1.36.6-CE-ubuntu24.04-r5)
- Image: [`docker.io/readystack/kubescheduler:v1.36.6-CE-ubuntu24.04-r5`](https://hub.docker.com/r/readystack/kubescheduler/tags), manifest digest `sha256:96cc95e028f81d33577a396cf44b73530017a25dd5abedb2fb3d9036fcdb04f3`
- Product page: https://readystack.dev/products/kubescheduler
- Source and build recipe: https://readystack.dev/products/kubescheduler/recipe
- Build trail: https://readystack.dev/products/kubescheduler/trail/v1.36.6-CE-ubuntu24.04-r5
- Defects: this repository's Issues (defects recorded from 25 November 2026 on) and https://readystack.dev/defects.html?product=kubescheduler

## The image

One binary per image, as upstream's images: /usr/local/bin/kube-scheduler, started by kubeadm's static Pod manifest /etc/kubernetes/manifests/kube-scheduler.yaml (command: kube-scheduler --kubeconfig=/etc/kubernetes/scheduler.conf ...). The ENTRYPOINT is /go-runner (upstream's kube-log-runner); a Pod command replaces it. In the Kubernetes VM Stack it is mirrored into the local registry as <LOCAL_REGISTRY>/kube-scheduler:v1.36.6 and kubeadm pulls it from there.

## Who uses kube-scheduler and why

Cluster administrators and platform engineers who build and run their own Kubernetes control plane.

kube-scheduler decides where each new pod runs. It filters out nodes that cannot run the pod, scores the rest against resources and constraints such as affinity, and binds the pod to the highest-scoring node.

A typical use: A team deploys a memory-hungry service whose replicas must not share a node. kube-scheduler skips nodes without enough free memory, honours the anti-affinity rule and places each replica on a different suitable node; if no node fits, the pod waits until one does.

From the upstream project's own description: https://kubernetes.io/docs/concepts/scheduling-eviction/kube-scheduler/, https://kubernetes.io/docs/reference/command-line-tools-reference/kube-scheduler/.

## This release

- Docker Scout policy grade at release: **A** (94%), graded 2026-11-21 with `docker scout policy` (CLI 1.26.0, docker-default configuration).
  - Docker Scout counts 83 packages with copyleft licences (GPL, LGPL, MPL), all of them operating-system packages (it counts only packages whose licence it can read; the SBOM lists every component).
- Signed with ReadyStack's release key (fingerprint `04daa92344a52dbc`): https://readystack.dev/keys/cosign.pub.
- Runs as a non-root user (`999:999`).
- Attestations on the image: an SPDX SBOM and SLSA provenance v1.
- Rebuilt from this kit with network egress blocked on 2026-11-21; the rebuilt image digest is the published one.
- Base: Ubuntu 24.04.

The current grade, measured again every day, is on https://readystack.dev/products/kubescheduler.

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

— ReadyStack · https://readystack.dev
