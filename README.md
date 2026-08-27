# Build & Brew: The AI Dojo

[![License: MIT](https://img.shields.io/badge/license-MIT-1B4332?style=flat-square&labelColor=0d1117)](./LICENSE) [![Cohort](https://img.shields.io/badge/NextWork-Cohort-7B42BC?style=flat-square&labelColor=0d1117)](https://github.com/RoyPiring) [![Systems](https://img.shields.io/badge/systems-5-2F5233?style=flat-square&labelColor=0d1117)](./INDEX.md) [![Updated](https://img.shields.io/badge/updated-2026--08--26-264653?style=flat-square&labelColor=0d1117)](./INDEX.md)

> *Every week we build one real solutions engineering project together, live. The dojo is free, and beginners are welcome.*

Build & Brew is the NextWork live cohort, and I run it as the Build Master. Each week the room builds one real, end-to-end solutions engineering project together on the NextWork Discord. You can build along or just watch, and you never have to speak. Every build lands in a GitHub repo, finished or not. The point is plain: practice, and proof you can do the work.

## What each build drills

Every build takes one point of view, the high-performing solutions engineer, across twelve roles and three tiers from Foundation to Advanced. Three things are drilled until they are reflex:

- **Design before you build.** The decision records and the diagram come before the code.
- **Prove it or it did not happen.** Every build ends in a concrete check, never "it looks right."
- **Do not trust the confident answer.** Every build scores the AI and reports where it was wrong.

## Systems

- **[Ship Your First Card Through GitHub](./systems/ship-your-first-card-through-github/)**: the delivery loop drilled once, a sourced and honest one-page card shipped through a repo, a board, a pull request, and a tagged release, with a second card proving the rails reuse.
- **[Terraform Module Testing and Decisions](./systems/terraform-module-testing-and-decisions/)**: a provable Terraform network module, a seeded boundary bug fixed to fail closed, an AI-drafted test matrix scored red to green, a clean Checkov scan, and a CI gate that runs with no cloud account.
- **[Build an Observable Health-Check Service](./systems/observable-health-check-service/)**: liveness and readiness split into two endpoints with different code paths, proven by killing the backing store so /readyz returns 503 while /livez holds 200, with local OpenTelemetry traces and a graceful shutdown drain.
- **[SRE Error Budget Arithmetic](./systems/sre-error-budget-arithmetic/)**: burn-rate thresholds derived by hand and matched against Sloth-generated MWMB rules on all twelve values, then proven with promtool on two synthetic series so a 10% outage pages and a 0.2% leak only tickets.
- **[DORA Delivery Scoreboard with DuckDB](./systems/dora-delivery-scoreboard/)**: five DORA measures defined in SQL over a captured corpus, where the same deployments give 26.0h or 1.0h depending on where the clock starts, and a two-check AI gate had to pass before the rework rate was allowed out.

More land here as they are onboarded, each with its architecture diagram, an implementation map, and the check that proved it works. Browse the catalog in [`INDEX.md`](./INDEX.md).

## The road to 100

**5 shipped, 95 to go.** One project a week, built live, in the open, toward 100.
