# Platform Ops Review — Cluster Snapshot

## Cluster Context

- **Current Kubernetes context:** `default`
- **Kubernetes version:** v1.30.4+k3s1 (control-plane node `95ffc31c`, Ubuntu 22.04.5 LTS, containerd 1.7.20-k3s1)

## Namespaces (4)

| Namespace | Deployments |
|---|---|
| `default` | (none) |
| `kube-node-lease` | (none) |
| `kube-public` | (none) |
| `kube-system` | `coredns` (1/1 ready, image rancher/mirrored-coredns-coredns:1.10.1), `local-path-provisioner` (1/1 ready, image rancher/local-path-provisioner:v0.0.28) |

**Workload summary:** the only deployments in the cluster are the two k3s system addons in `kube-system`. All other namespaces are empty. Services present: `kubernetes` (default, ClusterIP 443) and `kube-dns` (kube-system, ClusterIP 53).

## Audit Sheet vs. Live Cluster

Source: "Kubernetes Security Audit" spreadsheet — 5 tabs: Week1 (5 items), Week2 (6 items), Week3 (0 items, header only), Port-forward checks (5 items), Node Risk Summary (4 items). A "Live Status" column was appended to each tab.

**Outcome: 0 of 20 audit items matched the live cluster. Every item is `not found`.**

### Week1 — container risk audit (5 items, all `not found`)

| Namespace | Pod | Live Status |
|---|---|---|
| default | web-app-1-5c7d9f4f8c-xtj9p | not found — pod absent from namespace `default` (privileged: True, risk 10) |
| production | monitoring-agent-7b8594cbdd-fmmkw | not found — namespace `production` does not exist in cluster |
| dev | build-runner-68c9ddf6cc-wr7j8 | not found — namespace `dev` does not exist in cluster |
| staging | diag-tools-74b6d4c8f5-hp2mn | not found — namespace `staging` does not exist in cluster |
| test | net-tapper-6f97c7b9d4-zx1km | not found — namespace `test` does not exist in cluster |

### Week2 — container risk audit (6 items, all `not found`)

| Namespace | Pod | Live Status |
|---|---|---|
| production | time-sync-85b9fbb9c9-p9f6t | not found — namespace `production` does not exist in cluster |
| production | payment-gateway-77c5fdbdf4-nt2xz | not found — namespace `production` does not exist in cluster |
| staging | auth-service-66d8c5c97c-hrx8m | not found — namespace `staging` does not exist in cluster |
| dev | inventory-api-748f6c7d5d-bk1hz | not found — namespace `dev` does not exist in cluster |
| test | cache-service-6b8d7d85f6-t1mqf | not found — namespace `test` does not exist in cluster |
| test | search-engine-7f9b6dfb6b-r2vnx | not found — namespace `test` does not exist in cluster |

### Week3 (0 items)

Header row only; no audit items recorded.

### Port-forward checks (5 items, all `not found`)

| Check | Recorded Result | Live Status |
|---|---|---|
| Deployment ready | PASS | not found — deployment/web-status not present in namespace demo-ns (namespace absent) |
| Pod running | PASS | not found — pod web-status-6647894d84-627kr not present in cluster |
| Service endpoint registered | PASS | not found — service web-status not present in cluster |
| Page loads through the forwarded port | PASS | not found — no web-status workload exists in cluster to forward a port to |
| Marker text found | PASS | not found — no web-status page is served by the live cluster |

### Node Risk Summary (4 items, all `not found`)

| Node | Live Status |
|---|---|
| alpha | not found — node alpha not present in cluster (live node: 95ffc31c) |
| bravo | not found — node bravo not present in cluster (live node: 95ffc31c) |
| charlie | not found — node charlie not present in cluster (live node: 95ffc31c) |
| TOTAL | not found — totals reference nodes alpha/bravo/charlie, none of which exist in cluster |

## Interpretation

The audited environments (namespaces production/dev/staging/test, demo-ns, and nodes alpha/bravo/charlie) do not exist in the `default` context this snapshot was taken from. The audit sheet appears to describe a different cluster or a cluster state that has since been torn down/rebuilt. Recorded PASS results on the Port-forward checks tab cannot be reproduced against the live cluster. Follow-up action items have been filed as GitHub issues in this repository.
