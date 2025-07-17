Below is a **ready‑to‑save Markdown file** (`INTEGRATE_BMAD_V4.md`).
Drop it into the root of the `evolv3ai‑bmad‑claude‑code` repository and point Codex (or any other automation) to it—the file contains everything needed to perform the upgrade.

---

````markdown
# Integrate `evolv3ai‑bmad‑claude‑code` with BMAD‑METHOD v4.x

> **Scope**  
> Bring the forked BMAD v3 assets up to the current BMAD‑METHOD v4 release while **preserving all Claude‑Code‑specific enhancements**.

---

## 0  Prerequisites

* **Node 20 LTS or newer** – the v4 installer is an NPX tool. :contentReference[oaicite:0]{index=0}  
* **Git** – create an integration branch and allow easy rollback.  
* **Backup** – tag the current `main` and zip the repo before you start.

---

## 1  Create a Safety Branch

```bash
git checkout -b chore/upgrade-bmad-v4
````

---

## 2  Quarantine Legacy v3 Assets

All v3 BMAD artifacts live under `.bmad-core/` (plus docs). 

```bash
mv bmad-agent legacy-bmad-v3
```

---

## 3  Install BMAD‑METHOD v4 Core

```bash
npx bmad-method@latest install --ide=claude-code
```

The installer copies a fresh **`.bmad-core/`** tree, Claude‑Code command files, an install manifest, and a default `core-config.yaml`. 

---

## 4  Configure `core-config.yaml`

Update paths so v4 agents can still find existing documents:

```yaml
coreProjectLocation:
  prd:
    prdFile: docs/prd.md
    prdVersion: v3      # switch to v4 after migration
    prdSharded: false
  architecture:
    architectureFile: docs/architecture.md
    architectureVersion: v3
    architectureSharded: false
  devLoadAlwaysFiles:
    - docs/bmad-journal.md
```

---

## 5  Re‑introduce Fork‑Specific Content

| What                                  | Action                                                   |
| ------------------------------------- | -------------------------------------------------------- |
| Custom templates / tasks / checklists | Copy from `legacy-bmad-v3/**` into `.bmad-core/custom/`  |
| `CLAUDE‑ENHANCED.md`, other docs      | Keep at repo root – v4 Claude rules still reference them |
| Old setup scripts (`setup-bmad.sh`)   | Deprecate – document new NPX install path                |

---

## 6  Fix Hard‑Coded Paths

Search & replace `.bmad-core/` → `.bmad-core/`

```bash
grep -rl ".bmad-core/" . | xargs sed -i '' 's|.bmad-core/|.bmad-core/|g'
```

---

## 7  Smoke‑Test

```bash
npx bmad-method doctor        # integrity check
# then in VS Code:
# /dev  →  "Show project status"
```

Claude‑Code should load `.bmad-core` without errors.

---

## 8  Migrate Documents v3 → v4

For each doc (PRD, architecture, stories):

1. Ask the appropriate agent to **“convert \<doc> to v4 format and shard it.”**
2. Move the shards to `docs/…` and flip the corresponding `*Version` to `v4` plus `*Sharded: true` in `core-config.yaml`.

---

## 9  (Optional) Enable Semantic‑Release CI

If you want auto version bumps:

```bash
cp .bmad-core/common/workflows/release.yml .github/workflows/
```

Follow conventional‑commits (`fix:`, `feat:`).

---

## 10  Clean‑Up & Merge

```bash
git add .
git commit -m "feat!: integrate BMAD‑METHOD v4 core"
git push origin chore/upgrade-bmad-v4
```

Open a PR → review → merge → delete `legacy-bmad-v3/` after full migration.

---

### Appendix A  Legacy vs v4 Path Map

| v3 Path                      | v4 Path                      |
| ---------------------------- | ---------------------------- |
| `.bmad-core/templates/*.md`  | `.bmad-core/templates/*.md`  |
| `.bmad-core/tasks/*.md`      | `.bmad-core/tasks/*.md`      |
| `.bmad-core/checklists/*.md` | `.bmad-core/checklists/*.md` |
| `.bmad-core/data/bmad-kb.md` | `.bmad-core/data/bmad-kb.md` |

---

### Appendix B  Rollback

```bash
git checkout main
git reset --hard <pre‑upgrade‑tag>
rm -rf .bmad-core core-config.yaml bmad-manifest.json
mv legacy-bmad-v3 bmad-agent
```

---

**You’re done!** Your fork now runs on BMAD‑METHOD v4 while retaining all Evolv3AI Claude‑Code features.

```

---

Save the block above as `INTEGRATE_BMAD_V4.md` and hand it to Codex (or run it yourself).
```
