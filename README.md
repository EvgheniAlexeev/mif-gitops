# MIF GitOps Central Repository

Centralized deployment manifests for MIF microservices.

**Structure:**
- `base/` — Kustomize base configs (PaymentService, IdentityService)
- `overlays/` — Environment-specific overrides (dev, staging, prod)
- `app-of-apps/` — ArgoCD ApplicationSet for coordinated rollout
- `.github/workflows/` — CI/CD pipelines (merge → deploy)

**Workflow:**
1. Each solution repo (PaymentServices, IdentityServices) has its own git history
2. Changes merged to main → webhook triggers CI/CD
3. CI/CD commits deployment manifests to this repo (via bot)
4. ArgoCD watches this repo → automatically deploys to K8s

**Polyrepo + Centralized Pattern:**
- PaymentServices.sln: /path/to/payment-service/ (independent)
- IdentityServices.sln: /path/to/identity-service/ (independent)
- GitOps Central: /path/to/gitops-central/ (coordinates deployments)
