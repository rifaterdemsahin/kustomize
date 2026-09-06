# Kustomize

Kustomize in Kubernetes implementation — a working example of running Nginx in Minikube with Kustomize bases and overlays, plus a short animation explaining the pattern.

**Live site:** https://rifaterdemsahin.github.io/kustomize/

## Why SREs need to know Kustomize

Kustomize is one of the most common ways real teams manage Kubernetes manifests across environments, and it ships built into `kubectl` (`kubectl apply -k`) — no extra tooling to install or trust.

- **It's how environment drift actually gets caught.** Without a base/overlay structure, dev, staging, and prod each get their own full copy of every manifest. A change to a shared setting (a probe, a resource limit, a label) has to be made N times, and it's easy for one environment to silently fall out of sync with the others. SREs are the ones who get paged when that drift causes a prod-only incident that never showed up in staging.
- **It keeps configuration auditable and diffable.** Overlays hold only the lines that differ per environment. That means `git diff` on an overlay tells you exactly what changed for that environment — not a full re-read of a duplicated file — which matters during incident review and change approval.
- **It's part of the standard promotion pipeline.** Most GitOps workflows (Argo CD, Flux) render manifests with Kustomize before applying them. Understanding strategic merge (how patches match resources by `kind`/`name`, and how list items like containers merge by name) is required to safely write or review a patch, and to debug why a patch didn't apply the way someone expected.
- **It avoids the failure modes of templating engines.** No custom templating language, no values files holding logic, no rendering step to remember before `apply`. That reduces the surface area for outages caused by a broken template render or a missing default.
- **Rollback and blast-radius reasoning gets easier.** Because the base is the single source of truth and overlays are small, an SRE can reason precisely about what a given `kubectl apply -k overlays/<env>` will change, and revert a single overlay without touching the base or other environments.

## What's in this repo

- `1_Journey` → `7_Semblance` — project structure and setup notes for running Nginx via Kustomize in Minikube
- `assets/videos/` — a 90s animation (split into two clips) walking through the base/overlay pattern, embedded in `index.html`
- `index.html` — the project's GitHub Pages site, with the video and a written explanation of why Kustomize is needed
