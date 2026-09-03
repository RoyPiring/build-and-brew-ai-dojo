# Build & Brew: The AI Dojo · Catalog

> *A free weekly live cohort. One real AI project built together, toward the high-performing solutions engineer.*

The dojo runs a menu of 100 ranked builds across the twelve solutions-engineering roles, in do-first order, from Foundation to Core to Advanced. Each build ships four things: a validated working system, a stakeholder readout, a teach-back, and an honest after-action review. This catalog lists what has shipped so far.

## Systems

| # | System | What it drills | What it proves |
|--:|---|---|---|
| 1 | [Ship Your First Card Through GitHub](./systems/ship-your-first-card-through-github/) | The GitHub delivery loop | A sourced, honest one-page card shipped through a repo, a board, a pull request, and a tagged release, with a second card proving the rails reuse |
| 2 | [Terraform Module Testing and Decisions](./systems/terraform-module-testing-and-decisions/) | Software and platform engineering | A Terraform network module proven fail-closed: a seeded CIDR boundary bug caught by an AI-drafted test matrix, fixed with a validation block red to green, scanned clean by Checkov, and gated in CI with no cloud account |
| 3 | [Build an Observable Health-Check Service](./systems/observable-health-check-service/) | Service reliability and observability | Liveness split from readiness and proven under a real dependency kill: /readyz drops to 503 while /livez holds 200, so a store outage never reads as process failure, closed out with a graceful shutdown drain |
| 4 | [SRE Error Budget Arithmetic](./systems/sre-error-budget-arithmetic/) | Site reliability and error-budget policy | Four burn rates and eight windows derived by hand from the 99.9% objective and matched against the Sloth-generated rules on all twelve values, then evaluated by promtool so a 10% outage pages at 1 hour while a 0.2% leak tickets at 6 hours and wakes nobody |
| 5 | [DORA Delivery Scoreboard with DuckDB](./systems/dora-delivery-scoreboard/) | Delivery measurement and metric integrity | Five DORA metrics defined in SQL over a fixed 20-deployment corpus so every figure traces to its rows: the same data returns 26.0h, 18.0h or 1.0h of lead time depending on the starting clock, and a gate checking both record-level accuracy at 20 of 20 and the true count at 3 of 3 released the 15.0% rework rate, while a real-repository run exposed a 0.0h lead time that existed only because deploy time was copied from commit time |
| 6 | [Build a Typed Crossplane v2 API](./systems/typed-crossplane-platform-api/) | Platform engineering and API design | A namespaced Crossplane v2 ApplicationEnvironment API proven in both directions: CEL admission refused an unpinned image on a server-side dry run so nothing was stored and no composed resource work began, while deleting the queue-api Deployment returned NotFound and Crossplane restored it to 1/1 with no second apply. A legacy v1 XRD still passed admission at exit code 0, so only a scope script reading the live XRD and printing Namespaced confirmed the current pattern |

**6 of 100 shipped.** The rest arrive one live build at a time.
