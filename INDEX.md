# Build & Brew: The AI Dojo · Catalog

> *A free weekly live cohort. One real AI project built together, toward the high-performing solutions engineer.*

The dojo runs a menu of 100 ranked builds across the twelve solutions-engineering roles, in do-first order, from Foundation to Core to Advanced. Each build ships four things: a validated working system, a stakeholder readout, a teach-back, and an honest after-action review. This catalog lists what has shipped so far.

## Systems

| # | System | What it drills | What it proves |
|--:|---|---|---|
| 1 | [Ship Your First Card Through GitHub](./systems/ship-your-first-card-through-github/) | The GitHub delivery loop | A sourced, honest one-page card shipped through a repo, a board, a pull request, and a tagged release, with a second card proving the rails reuse |
| 2 | [Terraform Module Testing and Decisions](./systems/terraform-module-testing-and-decisions/) | Software and platform engineering | A Terraform network module proven fail-closed: a seeded CIDR boundary bug caught by an AI-drafted test matrix, fixed with a validation block red to green, scanned clean by Checkov, and gated in CI with no cloud account |
| 3 | [Build an Observable Health-Check Service](./systems/observable-health-check-service/) | Service reliability and observability | Liveness split from readiness and proven under a real dependency kill: /readyz drops to 503 while /livez holds 200, so a store outage never reads as process failure, closed out with a graceful shutdown drain |

**3 of 100 shipped.** The rest arrive one live build at a time.
