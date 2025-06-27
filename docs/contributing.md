
## 🗺️ Branching Model (Solo-Optimized “Trunk-Plus”)

| Branch                      | Purpose                                                | Merge rule                                                  | Protection                    |
| --------------------------- | ------------------------------------------------------ | ----------------------------------------------------------- | ----------------------------- |
| **`main`**                  | Always deployable / npm-publishable. Tags live here.   | Only via PR (yes—open a PR to yourself for review history). | ✅ Protected (require CI pass) |
| **`feat/*`**                | One feature or doc effort. Short-lived (hours → days). | Rebase or squash → `main`.                                  | ❌                             |
| **`fix/*`**                 | Bug fixes. Same flow as features.                      | Squash → `main`.                                            | ❌                             |
| **`chore/*`**, **`docs/*`** | Non-code tasks (lint config, docs, CI scripts).        | Squash → `main`.                                            | ❌                             |
| **`hotfix/*`**              | Emergency patches *after* a release tag.               | Merge → `main` + immediately tag patch.                     | optional                      |
| **`release/*`** (rare)      | Temporary branch if you need to hard-freeze for RC.    | Merge → `main` at GA.                                       | optional                      |

> *Why not `develop`?* Trunk-based (single long-lived `main`) keeps overhead low for one person.

---

## 🌱 Branch Naming Conventions

```
feat/websocket-heartbeat
fix/race-condition-reconnect
docs/quick-start
chore/semantic-release-setup
hotfix/0.1.1-crash
```

---

## 🔧 Commit Style

* **Conventional Commits** (`feat: …`, `fix: …`, `docs: …`, etc.).
  Enables *semantic-release* to auto-bump versions & generate CHANGELOG.
* One logical change per commit; squash commits in the PR so `main` stays linear.

---

## 🚦 Pull-Request Ritual (Yes, even solo)

1. Push branch.
2. Open PR → base **`main`**.
3. Checklist in description (tests, docs, CI green).
4. Self-review (GitHub lets you approve your own PR).
5. **Squash & merge** → `main` (keeps a tidy linear history).

> 💡 Tip: Use the PR template to remind yourself of docs/tests tasks.

---

## 🏷️ Versioning & Releases

* **semantic-release** drives version bumps & tags on every merge to `main`.
* Follow **SemVer**:

  * `feat:` → **minor** (`0.2.0`)
  * `fix:` → **patch** (`0.2.1`)
  * `BREAKING CHANGE:` footer → **major** (`1.0.0` when you’re ready)
* npm publish runs automatically in CI (only on `main`).

---

## 🔒 Branch Protection Rules (GitHub)

* **`main`**

  * Require status checks (CI) ✔️
  * Require PR ✔️
  * Dismiss stale approvals (optional)
* Everyone else (you!) can push to feature branches freely.

---

## 🔄 Typical Day-to-Day Flow

```bash
# new feature
git switch -c feat/kafka-batching
# work work work …
git add .
git commit -m "feat(kafka): add configurable batching interval"
git push -u origin HEAD
# open PR on GitHub, self-review, squash & merge
git switch main && git pull
# CI tags v0.x.y, publishes to npm@next
```

---

## 🆘 Hotfix Flow (post-release crash)

```bash
git switch -c hotfix/0.1.1-crash
# patch…
git commit -m "fix(core): avoid WS null deref on reconnect"
git push -u origin HEAD
# open PR → main, squash & merge
# semantic-release bumps to 0.1.1 and publishes as latest
```

---

## 📑 Automation Checklist

| Tool                      | Why                                                              | Config snippet                    |
| ------------------------- | ---------------------------------------------------------------- | --------------------------------- |
| **commitlint + Husky**    | Enforce Conventional Commits                                     | `@commitlint/config-conventional` |
| **semantic-release**      | Auto version, tag, CHANGELOG, npm publish                        | `release.config.js`               |
| **GitHub Actions**        | CI: `pnpm install`, `pnpm test`, `pnpm lint`, `semantic-release` | `.github/workflows/ci.yml`        |
| **Renovate / Dependabot** | Keep deps fresh                                                  | `renovate.json`                   |

---

### When to break the rules?

* Multi-day spikes? Push WIP commits; squash at merge.
* Need a long-running experimental line? Prefix with `exp/` and merge once it’s green.

