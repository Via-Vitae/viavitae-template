# viavitae-template

[![CI](https://github.com/Via-Vitae/viavitae-template/actions/workflows/ci.yml/badge.svg)](https://github.com/Via-Vitae/viavitae-template/actions/workflows/ci.yml)
[![Compliance](https://github.com/Via-Vitae/viavitae-template/actions/workflows/compliance-check.yml/badge.svg)](https://github.com/Via-Vitae/viavitae-template/actions/workflows/compliance-check.yml)
[![CodeQL](https://github.com/Via-Vitae/viavitae-template/actions/workflows/codeql.yml/badge.svg)](https://github.com/Via-Vitae/viavitae-template/actions/workflows/codeql.yml)
[![Licence](https://img.shields.io/badge/licence-Proprietary-0E1B3D?labelColor=F7F4EC)](LICENSE)
[![EU hosted](https://img.shields.io/badge/hosted-EU-0E1B3D?labelColor=F7F4EC)](SECURITY.md)

> Enterprise-grade repository template for regulated, EU-hosted software delivery.

`viavitae-template` is the single source of truth for how a ViaVitae repository looks.
Every new repository in the `Via-Vitae` organisation is generated from it, so
governance, security scanning, CI, privacy assessment and documentation standards are
present on day one instead of being retrofitted after an incident.

ViaVitae builds church-vertical websites, e-commerce and a marketplace for an EU pilot
in Lithuania, on self-hosted Proxmox infrastructure. Personal data stays in the EEA.

---

## Table of Contents

- [Generate a repository from this template](#generate-a-repository-from-this-template)
- [Governance rules R1-R5](#governance-rules-r1-r5)
- [Repository layout](#repository-layout)
- [Repo-type adaptation checklist](#repo-type-adaptation-checklist)
- [Development workflow](#development-workflow)
- [Quality gates](#quality-gates)
- [Security and compliance](#security-and-compliance)
- [Documentation](#documentation)
- [Licence](#licence)

---

## Generate a repository from this template

This repository is marked **Use this template** on GitHub. Never copy files by hand from
an existing project and never `git clone` a sibling repository as a starting point — both
bypass rule R1 and drift from the current baseline.

### 1. Create the repository

```bash
gh repo create Via-Vitae/viavitae-<name> \
  --template Via-Vitae/viavitae-template \
  --private \
  --description "<one-line purpose of the repository>"
```

Or in the browser: **Use this template** -> **Create a new repository** -> owner
`Via-Vitae` -> check **Include all branches**: no, `main` only.

### 2. Clone and configure

```bash
git clone git@github.com:Via-Vitae/viavitae-<name>.git
cd viavitae-<name>
git remote add upstream git@github.com:Via-Vitae/viavitae-template.git
```

### 3. Post-generation checklist

Complete every item before the first merge to `main`. Each is verifiable by the
`compliance-check.yml` workflow.

| # | Action | Where |
| --- | --- | --- |
| 1 | Replace `<name>` and the description in the README badge block and title. | `README.md` |
| 2 | Set the copyright entity and confirm the EU/LT jurisdiction clause applies. | `LICENSE` |
| 3 | Confirm `security@viavitae.com` and the scope list cover the new service. | `SECURITY.md` |
| 4 | Point path rules at the directories this repository actually contains. | `.github/CODEOWNERS` |
| 5 | Delete ecosystems that do not apply, keep the rest weekly. | `.github/dependabot.yml` |
| 6 | Adjust the language matrix to the repository stack. | `.github/workflows/codeql.yml` |
| 7 | Record the initial architecture decision as `ADR-001`. | `docs/architecture.md` |
| 8 | If any personal data is processed, open `DPIA-001` for this repository. | `docs/DPIA-template.md` |
| 9 | Enable branch protection per R4 and confirm required checks match `ci.yml` job names. | Repository settings |
| 10 | Run the local quality gates and push. | `CONTRIBUTING.md` |

### 4. Keeping the template in sync

Template improvements flow **outward only**. To pull a later template revision into an
existing repository, fetch `upstream` and merge `upstream/main`, resolving conflicts in
favour of the repository's own application code. Never edit the template inside a
consumer repository.

## Governance rules R1-R5

These five rules are binding on every repository generated from this template. A pull
request that violates one is blocked by review, not by convention.

### R1 - Template-first

Every ViaVitae repository is generated from `viavitae-template`. Governance files are not
hand-written per project, and a repository that lacks them fails the compliance gate.
Divergence from the template requires an accepted ADR in `docs/architecture.md` stating
the reason and the review date.

### R2 - CODEOWNERS immutability

`.github/CODEOWNERS` is never empty and always resolves to at least
`@Via-Vitae/architects` for `*`. Ownership overrides for narrower paths are permitted;
weakening or removing an existing rule is not. Any change that removes an owner, widens
`*`, or touches `/SECURITY.md`, `/LICENSE` or `/.github/CODEOWNERS` itself requires
approval from the compliance team in addition to the standard reviewers.

### R3 - PR gates

No change reaches `main` without: a Conventional Commit title, a linked issue where one
exists, one approving architect review, a green CI run, a green compliance run, and a
resolved CodeQL scan. Pull requests stay small — under 400 changed lines — because review
quality collapses above that threshold. Force-pushes to `main` and review dismissal are
disabled.

### R4 - Branch protection and secrets policy

`main` is protected: required status checks, required linear history, required review, no
force-push, no deletion, and administrators are not exempt. Secrets never enter the
repository in any form — not in code, not in CI configuration, not in test fixtures, not
in commit history. Gitleaks scans the **full history** on every pull request and fails on
any finding. CI jobs receive a least-privilege `GITHUB_TOKEN`: `contents: read` by
default, escalated per job only where the job genuinely needs it. Third-party actions are
pinned to full-length commit SHAs so a compromised upstream tag cannot alter the pipeline.

### R5 - DPIA requirement and licensing

Processing personal data requires a completed Data Protection Impact Assessment under
GDPR Article 35 **before** the processing starts, recorded in `docs/DPIA-template.md` and
referenced from the pull request. Data storage and third-party processors must be
EU/EEA-only; a non-EEA processor halts the work under QODER rule 7. All code is
proprietary — All Rights Reserved, (c) ViaVitae IT Technologies. Inbound third-party
components must carry an allow-listed licence; unknown or copyleft licences fail the
compliance gate and require a documented legal decision.

## Repository layout

```text
viavitae-template/
+-- README.md                     # This file - generation procedure and governance
+-- LICENSE                       # Proprietary - All Rights Reserved, EU/LT jurisdiction
+-- SECURITY.md                   # Disclosure policy, SLA table, safe harbour, scope
+-- QODER.md                      # Behavioural guidelines for AI-assisted coding
+-- CONTRIBUTING.md               # Contribution workflow, PR rules, DCO sign-off
+-- CHANGELOG.md                  # Keep a Changelog driven by Conventional Commits
+-- .gitignore                    # Node + Python + Terraform superset
+-- .editorconfig                 # Deterministic formatting across editors
+-- .github/
|   +-- CODEOWNERS                # Owner mapping, R2 baseline
|   +-- dependabot.yml            # Weekly grouped updates, five ecosystems
|   +-- PULL_REQUEST_TEMPLATE.md  # PR checklist including compliance items
|   +-- ISSUE_TEMPLATE/
|   |   +-- bug_report.yml        # Structured bug report form
|   |   +-- feature_request.yml   # Structured feature request form
|   +-- workflows/
|       +-- ci.yml                # Lint, typecheck, test, SAST, dependency scan
|       +-- compliance-check.yml  # Secrets, licences, governance file presence
|       +-- codeql.yml            # Static analysis, Python + TypeScript matrix
+-- docs/
    +-- architecture.md           # MADR architecture decision records, ADR-NNN
    +-- DPIA-template.md          # GDPR Article 35 assessment, nine sections
```

## Repo-type adaptation checklist

The template is stack-agnostic. Trim it to the repository type immediately after
generation — an unused gate is noise that trains reviewers to ignore failures.

### web (Next.js / TypeScript frontend)

- Keep: `ci.yml`, `codeql.yml` (`javascript-typescript`), compliance, Dependabot `npm`.
- Drop: Python jobs from `ci.yml`; Dependabot `pip`; Terraform and Docker ecosystems.
- Add: `axe` accessibility gate (WCAG 2.2 AA is mandatory under R3), Lighthouse budget,
  visual regression baseline, i18n parity check for `lt` / `en` / `ru`.

### api (Python / FastAPI backend)

- Keep: `ci.yml`, `codeql.yml` (`python`), compliance, Dependabot `pip` and `docker`.
- Drop: Node jobs; `javascript-typescript` from the CodeQL matrix.
- Add: coverage gate at 80%, contract tests, database migration up/down check,
  `DPIA-001` for every endpoint touching personal data.

### infra (Terraform / Ansible / Kubernetes)

- Keep: `ci.yml` lint stage, compliance, Dependabot `terraform`, `docker`,
  `github-actions`.
- Drop: application test and coverage stages; both CodeQL languages unless Actions are
  analysed, in which case keep `actions`.
- Add: `tflint` and `checkov`, `terraform plan` on PR with no apply, state locking
  verification, and confirm `*.tfstate*` stays ignored — state can contain secrets.

### docs (documentation and compliance hub)

- Keep: compliance, link checker, markdown lint, Dependabot `github-actions`.
- Drop: build, test, typecheck, SAST stages and both CodeQL languages.
- Add: published-site deploy gate, ADR index consistency check, DPIA register index.

### qa (shared testing assets)

- Keep: `ci.yml` lint and test stages, compliance, Dependabot `npm` and `pip`.
- Drop: build artifact stage; application coverage gate (this repo ships test assets).
- Add: nightly end-to-end schedule, test-data provenance check confirming no production
  personal data is stored in fixtures.

## Development workflow

1. Branch from `main` using a conventional prefix: `feat/`, `fix/`, `chore/`, `docs/`.
2. Commit using Conventional Commits — the changelog is derived from these messages.
3. Self-review against `QODER.md` if you used AI assistance.
4. Open a pull request; the template pre-fills the compliance checklist.
5. Pass the gates: CI, compliance, CodeQL, and one architect review.
6. Squash and merge. Trunk-based: no long-lived feature branches.

See [CONTRIBUTING.md](CONTRIBUTING.md) for the full workflow and the DCO sign-off line.

## Quality gates

Every pull request must pass:

| Gate | Tool | Threshold |
| --- | --- | --- |
| Lint | `ruff`, `eslint` | zero findings |
| Format | `ruff format`, `prettier` | no diff |
| Types | `mypy --strict`, `tsc --noEmit` | zero errors |
| Unit tests | `pytest`, `vitest` | pass, coverage at least 80% |
| SAST | Semgrep, CodeQL | zero findings at or above the failure severity |
| Dependencies | Trivy filesystem | fail on `CRITICAL` |
| Secrets | Gitleaks, full history | fail on any finding |
| Licences | allow-list scan | unknown licence fails and is labelled for review |
| Governance | presence checks | CODEOWNERS, `.editorconfig`, `.gitignore` present |

## Security and compliance

- To report a vulnerability, follow the private disclosure process in
  [SECURITY.md](SECURITY.md). Do **not** open a public issue. Acknowledgement is within
  24 hours, triage within 72 hours.
- Personal-data processing requires a completed
  [DPIA](docs/DPIA-template.md) under GDPR Article 35 before processing starts (R5).
- Architectural choices with security, privacy or data-residency impact are recorded as an
  [ADR](docs/architecture.md).
- A GDPR personal-data breach is notified to the supervisory authority within 72 hours of
  awareness; see the breach workflow in [SECURITY.md](SECURITY.md).

## Documentation

| Document | Purpose |
| --- | --- |
| [SECURITY.md](SECURITY.md) | Disclosure policy, SLA table, safe harbour, scope. |
| [CONTRIBUTING.md](CONTRIBUTING.md) | Contribution workflow, PR rules, DCO sign-off. |
| [CHANGELOG.md](CHANGELOG.md) | Release history in Keep a Changelog format. |
| [QODER.md](QODER.md) | AI pair-programming guardrails and stop-conditions. |
| [docs/architecture.md](docs/architecture.md) | MADR decision records and index. |
| [docs/DPIA-template.md](docs/DPIA-template.md) | GDPR Article 35 assessment template. |

## Licence

Proprietary — All Rights Reserved. (c) ViaVitae IT Technologies. No redistribution, no
derivative works and no commercial use by third parties without a written agreement.
Governed by the law of Lithuania (EU). See [LICENSE](LICENSE).
