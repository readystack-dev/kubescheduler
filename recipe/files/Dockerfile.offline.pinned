# =====================================================================
# kube-scheduler v1.36.6 L-009 HERMETIC OFFLINE BUILD — the canonical, attestation-producing build. build_kit runs
#   docker buildx build --builder readystack-builder-max --network=none
#     --attest type=provenance,mode=max --attest type=sbom --push <context>
# Pinned-public-base + vendored model: golang 1.26.10 + ubuntu 24.04 pinned BY DIGEST (BASE_IMAGE_DIGESTS.lock);
# vendored/kubescheduler-src is kubernetes v1.36.6 fully vendored (GOPROXY=off, the workspace vendor/),
# vendored/debs the apt closure (dpkg offline).
# Dockerfile and Dockerfile.offline are byte copies of this file (the repo-wide convention).
#
# SHAPE (upstream build/server-image/Dockerfile at v1.36.6): /usr/local/bin/kube-scheduler, /go-runner (kube-log-runner,
# built from the same tree) as ENTRYPOINT. kubeadm's static Pod sets `command: [kube-scheduler, ...]`, which
# replaces the ENTRYPOINT. Unlike upstream (distroless, root) the base is Ubuntu 24.04 and the default user is kube
# (999:999): root comes from the Pod (securityContext.runAsUser: 0, the kubeadm patch file
# product-kits/kubevirt-stack/install/controlplane/patches/kube-scheduler+strategic.yaml).
# =====================================================================

# --- kube-scheduler + kube-log-runner (/go-runner) --------------------------------------------------------------------
FROM golang:1.26.11-bookworm@sha256:dc40a13b61304647ed7e5d581d246b86413dfc29d63c6d018310feb93cd98fc0 AS build
ARG SOURCE_DATE_EPOCH=1795244555
WORKDIR /k8s
COPY vendored/kubescheduler-src/ .
ENV CGO_ENABLED=0 GOTOOLCHAIN=local GOFLAGS=-buildvcs=false GOPROXY=off
# upstream hack/lib/golang.sh + version.sh: tags selinux,notest,grpcnotrace; -trimpath -s -w; static (CGO off);
# gitVersion/gitCommit/gitTreeState/gitMajor/gitMinor/buildDate stamped into both version packages, buildDate from
# SOURCE_DATE_EPOCH (the v1.36.6 commit's committer date). go.work + the workspace vendor/ (no network).
# The binary must link golang.org/x/net at or above v0.62.0, the floor Dockerfile.vendor applies (GO-2026-6860 /
# CVE-2026-78661, strategies/kubescheduler.json dep_bumps): a stale vendored tree fails here instead of shipping 0.61.0.
RUN V=v1.36.6 C=1b2171b110bb2a2542e1322035c6583ec86eb976 D=2026-10-27T12:02:52Z L="-s -w" && \
    for p in k8s.io/client-go/pkg/version k8s.io/component-base/version; do \
      L="$L -X $p.gitVersion=$V -X $p.gitCommit=$C -X $p.gitTreeState=clean -X $p.gitMajor=1 -X $p.gitMinor=36 -X $p.buildDate=$D"; \
    done && mkdir -p /out && \
    go build -trimpath -tags selinux,notest,grpcnotrace -ldflags "$L" -o /out/ \
      ./cmd/kube-scheduler ./staging/src/k8s.io/component-base/logs/kube-log-runner && \
    /out/kube-scheduler --version | grep -qx 'Kubernetes v1.36.6' && \
    for b in /out/kube-scheduler /out/kube-log-runner; do v=$(go version "$b" | awk '{print $2}'); \
      [ "$v" = go1.26.11 ] || { echo "$b was built with '$v', not go1.26.11 (the pin manifest's toolchain)" >&2; exit 1; }; done && \
    { x=$(go version -m /out/kube-scheduler | awk '$1 == "dep" && $2 == "golang.org/x/net" {print $3}'); \
      [ -n "$x" ] && [ "$(printf '%s\n%s\n' "$x" v0.62.0 | sort -V | head -n1)" = v0.62.0 ] || \
        { echo "/out/kube-scheduler links golang.org/x/net ${x:-(none)}, below its v0.62.0 floor" >&2; exit 1; }; }

# --- rs-probe: the health-check helper (standard library only, from the kit's rs-probe.go) ----------------------------
FROM golang:1.26.11-bookworm@sha256:dc40a13b61304647ed7e5d581d246b86413dfc29d63c6d018310feb93cd98fc0 AS build_rs_probe
WORKDIR /probe
COPY rs-probe.go main.go
COPY rs-probe.mod go.mod
ENV CGO_ENABLED=0 GOTOOLCHAIN=local GOFLAGS="-buildvcs=false" GOPROXY=off
RUN go build -trimpath -ldflags='-s -w' -o /rs-probe . && \
    [ "$(go version /rs-probe | awk '{print $2}')" = go1.26.11 ]

FROM ubuntu:24.04@sha256:9c7f200c07e3d4a03e7249d2c16bf66a51120fb876aa2242cf31451412543289
# Offline apt: dpkg-install the vendored closure (CVE upgrades + runtime packages) with no network. Each pass installs
# one more Pre-Depends level; fail closed unless every vendored package is installed at its vendored version. The
# removed paths are wall-clock or host-derived (dpkg/apt logs, the ldconfig aux-cache). The installed apt,
# libapt-pkg6.0t64 and libpcre2-8-0 must meet strategies/kubescheduler.json apt_refresh (CVE-2026-109518 and the pcre2
# mediums): a stale vendored/debs fails here instead of shipping the base's older packages.
# vendored/debs: bind-mounted (read-only), so never a layer of the image (kit migration vendored-bind-mount)
RUN --mount=type=bind,source=vendored/debs,target=/tmp/debs \
    export DEBIAN_FRONTEND=noninteractive; \
    set --; for f in /tmp/debs/*.deb; do [ -e "$f" ] && set -- "$@" "$f"; done; \
    for pass in 1 2 3 4 5; do [ $# -eq 0 ] || dpkg -i --skip-same-version "$@" >/dev/null 2>&1 && break; done; \
    dpkg --configure -a && bad= && for d in "$@"; do p=$(dpkg-deb -f "$d" Package) v=$(dpkg-deb -f "$d" Version); \
      case "$(dpkg-query -W -f='${Version} ${db:Status-Abbrev}' "$p" 2>/dev/null)" in "$v ii"*) ;; *) bad="$bad $p=$v" ;; esac; done && \
    { [ -z "$bad" ] || { echo "vendored debs not installed at their vendored version:$bad" >&2; false; }; } && \
    for m in apt=2.8.4ubuntu0.1 libapt-pkg6.0t64=2.8.4ubuntu0.1 libpcre2-8-0=10.42-4ubuntu2.2; do p=${m%%=*} v=${m#*=}; \
      dpkg --compare-versions "$(dpkg-query -W -f='${Version}' "$p" 2>/dev/null)" ge "$v" || \
        { echo "$p is below $v, the CVE minimum of strategies/kubescheduler.json apt_refresh" >&2; exit 1; }; done && \
    rm -rf /var/lib/apt/lists/* \
           /var/log/dpkg.log /var/log/alternatives.log /var/log/apt \
           /var/cache/ldconfig/aux-cache /var/cache/fontconfig
# Reproducibility: /etc/shadow field 3 is days-since-epoch AT BUILD TIME, so it is frozen to SOURCE_DATE_EPOCH.
# ARG, never ENV: an ENV would persist into the image config and change the digest.
ARG SOURCE_DATE_EPOCH=1795244555
# kube (uid/gid 999) is the default user; kube-scheduler needs nothing it owns (kubeadm mounts its PKI and kubeconfig).
RUN groupadd -g 999 kube && useradd -r -u 999 -g kube -d /nonexistent -M -s /usr/sbin/nologin kube && \
    : "${SOURCE_DATE_EPOCH:?SOURCE_DATE_EPOCH must be set for a reproducible build}" && \
    sed -ri "s/^([^:]*:[^:]*):[0-9]*:/\\1:$((SOURCE_DATE_EPOCH/86400)):/" /etc/shadow && \
    rm -f /etc/passwd- /etc/shadow- /etc/group- /etc/gshadow-
COPY --from=build /out/kube-scheduler /usr/local/bin/kube-scheduler
COPY --chmod=0755 --from=build /out/kube-log-runner /go-runner
COPY --from=build_rs_probe /rs-probe /usr/local/bin/rs-probe
USER 999:999
ENTRYPOINT ["/go-runner"]
# >>> readystack OCI labels: strategies/kubescheduler.json image_labels plus the standard OCI labels,
# the set a generated kit carries (gen_dockerfile.label_lines). Written by scripts/kit_migrations.py;
# scripts/retarget.py re-renders it with every retarget. Never edit it by hand.
LABEL org.opencontainers.image.licenses="Apache-2.0"
LABEL org.opencontainers.image.title="kube-scheduler"
LABEL org.opencontainers.image.description="kube-scheduler v1.36.6 built from source for kubeadm clusters: the Kubernetes scheduler image, non-root by default, root only where the kubeadm static Pod sets it."
LABEL org.opencontainers.image.source="https://github.com/kubernetes/kubernetes"
LABEL org.opencontainers.image.version="v1.36.6-CE-ubuntu24.04-r5"
LABEL org.opencontainers.image.revision="1b2171b110bb2a2542e1322035c6583ec86eb976"
# <<< readystack OCI labels
