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

---

## 🔐 GRACE Test Gate (Pre-Push Hook)

All MIF service repos use a **pre-push hook** that blocks pushes when unit tests fail.
The canonical hook lives in this repo at `hooks/pre-push`.

### Install in a service repo

```bash
# From the service root (e.g., identity-service/, payment-service/)
ln -sf ../../hooks/pre-push .git/hooks/pre-push
```

Or copy it (if symlinks don't work on your setup):

```bash
cp /path/to/mif-gitops/hooks/pre-push .git/hooks/pre-push
chmod +x .git/hooks/pre-push
```

### What it does

- Runs `dotnet test` on push — **unit tests only** (filters out Integration tests that need Docker)
- If **any test fails** → push is blocked with a clear message
- Use `git push --no-verify` to bypass (not recommended — CI will catch it anyway)

### Troubleshooting

| Symptom | Fix |
|---------|-----|
| `dotnet: command not found` | Ensure .NET SDK is in `$PATH` (`dotnet --version`) |
| Hook not firing | Check permissions: `chmod +x .git/hooks/pre-push` |
| Symlink broken | Path is relative to `.git/hooks/`, so `../../hooks/pre-push` resolves to repo root's `hooks/pre-push` |

---

## 📁 Structure
