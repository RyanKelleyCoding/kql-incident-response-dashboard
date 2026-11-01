# Security Pipeline

This repo uses a free-first security workflow pack aimed at workbook, Bicep, PowerShell, and dependency safety without introducing expensive enterprise tooling.

## PR Gate

### Gitleaks

What it does: Scans for leaked secrets in source and workflow files.

Why it works: CI/CD secrets, tokens, and connection strings often have consistent formats that can be detected early before merge.

How to tune false positives: Replace sample secrets with placeholders and allow-list only reviewed examples.

### Trivy FS + Config

What it does: Scans repository files for dependency vulnerabilities and misconfigurations.

Why it works: It catches both vulnerable software components and risky configuration defaults in one pass.

How to tune false positives: Keep blocking at `HIGH,CRITICAL`, then add explicit ignores only when you understand the risk and why it is acceptable.

### pip-audit

What it does: Audits Python dependencies when Python manifests exist.

Why it works: Dependency advisory matching is a low-cost, high-value way to catch known vulnerable packages.

How to tune false positives: This is warn-only by default. Tighten later if the repo grows more Python surface area.

### npm audit

What it does: Audits Node dependencies when Node lockfiles exist.

Why it works: Frontend or build tooling risks often arrive through transitive packages.

How to tune false positives: Keep it warn-only until there is a stable remediation cadence.

### Bandit

What it does: Scans Python code for insecure coding patterns.

Why it works: Pattern-based Python checks catch many real issues at low cost.

How to tune false positives: Exclude tests and generated content, and suppress only specific reviewed findings.

## Nightly Scans

### Semgrep

What it does: Runs curated security rules across code and scripts.

Why it works: It catches unsafe code patterns and script issues that dependency scanners do not see.

How to tune false positives: Use the curated security ruleset and keep exclusions targeted.

### Checkov

What it does: Scans Bicep and GitHub Actions definitions for insecure infrastructure and workflow settings.

Why it works: For an Azure workbook repo, configuration risk is often more important than application-runtime risk.

How to tune false positives: Use explicit skip annotations with reasons for intentional exceptions.

### OSV-Scanner

What it does: Scans manifests and lockfiles recursively for known vulnerable dependencies.

Why it works: It uses the open OSV advisory ecosystem and is a strong free dependency-risk baseline.

How to tune false positives: Ignore advisories only when there is no fix or the affected component is unreachable, and document that decision.

## Recommended Operating Model

1. Block PRs on secrets and severe configuration issues.
2. Use nightly scans to discover backlog, not to interrupt every contributor.
3. Keep exceptions documented and minimal.

## Cost Profile

The current pack is based on free or open-source scanners. The main spend is Actions runtime, which remains low for a repo of this size.
