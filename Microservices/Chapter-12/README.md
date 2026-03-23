# CI/CD

CI/CD — automate everything from code commit to production deployment. No manual steps.

---

## 1. Pipeline: build → test → static analysis → Docker build → push → deploy

```
Developer pushes code
  → CI triggers
  → Build (compile, resolve dependencies)
  → Test (unit tests, integration tests)
  → Static Analysis (SonarQube — code quality, vulnerabilities, coverage)
  → Docker Build (multi-stage, tag with commit SHA or version)
  → Push (push image to registry — ECR, Docker Hub, GCR)
  → CD triggers
  → Deploy to staging (auto)
  → Run smoke tests / E2E tests
  → Deploy to production (auto or manual approval gate)
```

```
git push → GitHub Actions / Jenkins / GitLab CI
  ├── mvn clean verify (build + test)
  ├── sonar-scanner (quality gate)
  ├── docker build -t order-service:abc123 .
  ├── docker push ecr/order-service:abc123
  └── kubectl set image deployment/order-service order-service=ecr/order-service:abc123
```

Key practices:

- Tag images with commit SHA, not `latest` (reproducible, rollback-friendly)
- Fail fast — tests and analysis run early, don't waste time building a broken image
- Each service has its own pipeline (independent deploy)
- Quality gate — if coverage < 80% or critical bugs found, pipeline fails

---

## 2. GitOps (ArgoCD)

Desired state of your cluster is defined in Git. ArgoCD watches Git and auto-syncs the cluster to match.

```
Git Repo (k8s manifests / Helm charts):
├── order-service/deployment.yaml  (image: order-service:v1.2.0)
├── payment-service/deployment.yaml
└── infrastructure/

ArgoCD watches repo
  → detects change (image tag updated to v1.3.0)
  → auto-syncs → applies to K8s cluster
  → cluster now runs v1.3.0
```

```
Traditional CD:
  Pipeline → kubectl apply → cluster (push-based)

GitOps:
  Pipeline → update image tag in Git → ArgoCD detects → syncs to cluster (pull-based)
```

| Benefit | Why |
|---------|-----|
| Git is the source of truth | What's in Git = what's in the cluster |
| Audit trail | Every change is a Git commit (who, what, when) |
| Easy rollback | `git revert` → ArgoCD syncs → rolled back |
| Drift detection | ArgoCD alerts if cluster state differs from Git |
| Self-healing | Someone manually changes cluster → ArgoCD reverts it to match Git |

---

## 3. Trunk-Based Development vs Feature Branches

### Trunk-based

```
main ──●──●──●──●──●──●──●── (everyone commits to main, small frequent merges)
        \─●─/  \─●─/
        short-lived branches (hours, not days)
```

- Small, frequent commits to main
- Feature flags to hide incomplete work
- CI runs on every commit — always deployable
- Favored by high-performing teams (Google, Meta)

### Feature branches (GitHub Flow)

```
main ──●──────────●──────────●──
        \                   /
         feature/checkout ──● ── ● ── ● ── PR ── merge
         (lives for days/weeks)
```

- Branch per feature, merge via PR
- Code review before merge
- Can lead to long-lived branches → painful merges

| Aspect | Trunk-based | Feature branches |
|--------|-------------|-----------------|
| Branch lifetime | Hours | Days to weeks |
| Merge conflicts | Rare (small changes) | Common (big changes) |
| CI/CD speed | Fast (always deployable) | Slower (wait for PR review) |
| Risk | Needs feature flags, good tests | Merge hell, stale branches |
| Best for | Experienced teams, continuous deployment | Teams needing code review gates |

---

## 4. Canary Releases

Deploy new version to a small % of traffic. Monitor. Gradually increase if healthy.

```
Step 1: v2 deployed → 5% traffic
  → monitor error rate, latency, logs
  → looks good ✓

Step 2: increase to 25%
  → still good ✓

Step 3: increase to 50% → 75% → 100%
  → full rollout ✓

If at any step metrics degrade:
  → route 100% back to v1 → investigate
```

Ingress / Service Mesh (Istio):

```
95% → order-service v1 (stable)
 5% → order-service v2 (canary)
```

Istio traffic splitting:

```yaml
apiVersion: networking.istio.io/v1alpha3
kind: VirtualService
spec:
  http:
    - route:
        - destination:
            host: order-service
            subset: v1
          weight: 95
        - destination:
            host: order-service
            subset: v2
          weight: 5
```

### Canary vs blue-green

| Aspect | Canary | Blue-Green |
|--------|--------|------------|
| Traffic shift | Gradual (5% → 25% → 100%) | Instant (0% → 100%) |
| Risk | Low (small % affected) | Higher (all users switch at once) |
| Rollback | Shift traffic back | Switch back to old environment |
| Resources | Minimal extra (few v2 pods) | Double (full v2 environment) |

Canary is the safest deployment strategy for production — real users test the new version at low risk.
