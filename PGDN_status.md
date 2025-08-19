# PGDN Project Status

You can find open-source repos for for [pgdn.ai](http://pgdn.ai/)’s [here](https://github.com/pgdn-network).

**Status:** pre-release; parts are **not production ready** yet. We’re moving toward a runnable CLI that anyone can use. In parallel, we offer a **managed service** for operators who prefer a hosted workflow. 

We're still in early access.

- **What’s here:** analysis modules, parsers, sample outputs, methodology, anonymised findings.
- **Not ready yet:** one-command runner/installer, full docs, stable CLI flags.
- **Safety:** external observation only; no exploits. Use **only on assets you own or have explicit permission to test**.

**Need a quick overview?** See the [roadmap.md](roadmap.md).

**Operator?** See [validation.md](validation.md) to verify ownership and request managed access.

**Reporting a problem?** See [contact.md](contact.md) and [security.md](security.md).

## What works today

- Core analysis modules (outside-in checks for ports/RPC/metrics/TLS/WAF).
- Parsers and normalisers for scan outputs → posture findings.
- Sample anonymised dataset + findings reports for Sui snapshot (Aug ’25).
- Documentation of methods, limitations, and remediation guidance.

## What’s WIP

- Runnable CLI (`pgdn scan ...` and `pgdn report ...`) with safe defaults.
- Installer & packaging (pipx / containers) and reproducible environments.
- Operator self-checks and dispute flow (false-positive handling) via web form.
- Multi-network presets (Aptos/Arweave/etc.) and role-aware heuristics.
- Orchestration for long-running jobs (Temporal/Kubernetes) in the OSS runner.

## What’s out of scope (here)

- Exploit tooling and invasive testing.
- Private/credentialed assessments.
- Naming live operators in public artefacts.

## How to engage

- **Operators:** verify ownership → request managed access (see [validation.md](validation.md)).
- **Reviewers:** open issues/PRs for method quality, reproducibility, and docs.
