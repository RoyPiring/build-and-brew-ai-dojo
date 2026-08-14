<img src="https://cdn.prod.website-files.com/677c400686e724409a5a7409/6790ad949cf622dc8dcd9fe4_nextwork-logo-leather.svg" alt="NextWork" width="300" />

# Build an Observable Health-Check Service

**Project Link:** [View Project](https://nextwork.ai/projects/5feb991f-f1c2-43a8-b28f-eae00c0e087b)

**Author:** Roy Piring Jr: Sr. Cloud Engineer | Architect  
**Email:** rpiringhawaii@gmail.com

---

![Image](https://nextwork.ai/refreshed_maroon_timid_jujube/uploads/5feb991f-f1c2-43a8-b28f-eae00c0e087b_h3gf7ubd)

## Understanding the Alive vs Ready Problem

### Why the liveness/readiness distinction matters

The liveness and readiness split gave the service two different signals for two different operational decisions. Liveness answered whether the Flask process was running, while readiness answered whether the service could currently handle traffic that depended on the backing store.

That distinction mattered because a process can remain healthy while one of its dependencies is unavailable. Treating those conditions as the same failure could turn a temporary dependency outage into unnecessary process restarts.

Separate endpoints let the orchestrator remove an unhealthy instance from traffic while keeping the process alive long enough for the dependency to recover.

## Proving the Readiness Contract

![Image](https://nextwork.ai/refreshed_maroon_timid_jujube/uploads/5feb991f-f1c2-43a8-b28f-eae00c0e087b_immsixvs)

### The central assertion: liveness unaffected by a dead dependency

After I killed the store, /readyz failed its TCP connection and returned HTTP 503 with not_ready / store unreachable.

At the same time, /livez continued returning HTTP 200 with {"status":"alive"} because that endpoint never contacted the store.

That result proved the central contract. Traffic could stop flowing to the service while the Flask process remained alive, avoiding a restart storm caused by treating dependency failure as process failure.

## Building Two Health Endpoints with Genuinely Different Logic

### Implementing the service

In this step, I built store.py as a simple TCP backing service and app.py as the Flask application. The two health endpoints intentionally followed different code paths.

/livez returned HTTP 200 without opening a socket or checking any external dependency. /readyz attempted a TCP connection to the configured store and returned HTTP 200 only when the store was reachable, otherwise it returned HTTP 503.

I also instrumented requests with OpenTelemetry and exported spans as JSON lines to a local file. Required settings came from environment variables, the app failed immediately when required configuration was missing, and all communication with the store stayed on the TCP boundary.

![Image](https://nextwork.ai/refreshed_maroon_timid_jujube/uploads/5feb991f-f1c2-43a8-b28f-eae00c0e087b_h3gf7ubd)

### How /livez and /readyz differ

/livez only asked whether the Flask process could answer a request. It did not open a socket, call the store, or inspect any dependency, and it returned HTTP 200 with {"status":"alive"}.

/readyz opened a TCP connection to STORE_HOST:STORE_PORT with a 1-second timeout. A successful connection returned HTTP 200, while an unreachable store returned HTTP 503.

That difference was intentional. A store outage changed readiness without falsely reporting that the process itself was dead.

## Designing with AI Before Writing Code

### Why design artifacts come first

In this step, I created the design artifacts before implementing the service. The AI drafted the requirements brief, four ADRs, C4 container view, and build plan, then I reviewed those artifacts against the standing rules.

The review focused on the liveness/readiness contract because that distinction controlled the operational behavior of the system. The design needed to state clearly that liveness could never depend on the store.

I committed the corrected artifacts before the implementation phase so the code had an explicit architecture to follow instead of defining the architecture after the fact.

![Image](https://nextwork.ai/refreshed_maroon_timid_jujube/uploads/5feb991f-f1c2-43a8-b28f-eae00c0e087b_4x787m1g)

### Catching the AI's conflation of liveness and readiness

A common failure in AI-generated health-check designs is collapsing both signals into one /health endpoint or placing a dependency check inside liveness. Either choice can make a temporary store outage look like a dead application process.

The draft already separated the routes, but I still corrected ADR-01 so the contract could not be misread.

The final ADR stated that /livez checks only process health and touches no dependency, while /readyz opens a TCP connection to the store and returns HTTP 200 or 503 based on reachability. A combined /health endpoint was explicitly rejected.

## Setting Up the Observable Service Stack

### Project setup goals

In this step, I set up the development environment and build structure for the observable health-check service. The goal was to establish the dependencies, standing rules, environment contract, and repository controls before implementation.

The setup gave the service a repeatable configuration and made the operational rules visible to both me and any AI agent working in the repository.

This mattered because health-check behavior depends on small architectural details. The setup captured those details before they could drift during implementation.

### Standing rules, requirements, and environment config

requirements.txt pinned Flask and OpenTelemetry so the same dependency versions could be installed across environments. .env.example documented the four required environment variables: STORE_HOST, STORE_PORT, TRACE_FILE, and SERVICE_PORT.

standing-rules.md defined the Twelve-Factor expectations, the /livez and /readyz contract, and the requirement for local trace output. TASKS.md held the lab workflow.

The repository controls kept runtime artifacts out of source control. .gitignore excluded .key/ and traces.jsonl, .gitattributes enforced LF line endings, and .key/sealed-key.md remained a private placeholder that was not committed.

## Scoring the AI and Delivering the Readout

### Proving the drill, auditing AI claims, drafting the stakeholder readout

In this step, I ran the kill drill to prove that the two health signals remained independent when the backing store failed.

I stopped the store and verified that /readyz changed to HTTP 503 while /livez remained HTTP 200. That gave me direct evidence that the service could survive a partial outage without confusing dependency failure with process failure.

I also reviewed the AI-generated claims against the observed behavior and prepared the stakeholder readout from those results. The readout was based on what the drill proved rather than what the architecture was merely expected to do.

## Closing the Project with Defended Decisions

### What close-out covers

In this step, I closed the build with a teach-back, an after-action review, and the final performance evidence.

The teach-back required me to defend the architecture instead of only describing it. The after-action review captured the limits, corrections, and lessons that appeared while the service was tested.

The close-out finished with the verified v1.0.0 release so the design, implementation, drill evidence, and final documentation all pointed to the same delivered state.

![Image](https://nextwork.ai/refreshed_maroon_timid_jujube/uploads/5feb991f-f1c2-43a8-b28f-eae00c0e087b_b8ad2l8u)

### Four decisions defended in the teach-back

The first decision was splitting /livez and /readyz instead of using one /health endpoint. That kept a store outage from becoming a process restart problem.

The second decision was loading configuration from environment variables instead of a committed configuration file. That kept the service from being tied to one machine or environment. The third was using a killable local store process instead of an in-process mock or container so the dependency outage was real.

The fourth decision was writing OpenTelemetry spans to a local file instead of depending on a vendor agent. That kept tracing local, added no external runtime dependency, and produced output that could be inspected directly.

## Secret Mission: Graceful Shutdown Drain

![Image](https://nextwork.ai/refreshed_maroon_timid_jujube/uploads/5feb991f-f1c2-43a8-b28f-eae00c0e087b_jz1y1c5u)

### What the drain window proves about Factor IX Disposability

The base build proved Factor IX only through fast startup and crash-only shutdown. The process could stop quickly, but Ctrl+C caused an immediate exit.

The drain window added a cleaner shutdown path. SIGINT changed readiness first, causing /readyz to return HTTP 503 with a draining state while /livez remained HTTP 200 for two seconds.

That gave an orchestrator time to stop sending traffic before the process exited. The service could announce that it was leaving rotation while remaining alive long enough to finish the drain window.

## Reflections and Key Takeaways

### Tools and concepts learned

The key tools I used included Python, Flask, and OpenTelemetry. Python and Flask provided the service and health endpoints, while OpenTelemetry captured request traces.

The main concepts I learned included separating liveness from readiness, applying Twelve-Factor principles to runtime configuration and backing services, using dependency failure drills to prove health behavior, and adding graceful shutdown handling for orchestration-friendly disposal.

The larger lesson was that health checks are part of the service contract. Their value comes from accurately describing state under failure, not simply returning HTTP 200 when the application starts.

### Time and challenges

This build took me approximately 60 minutes.

The hardest part was implementing the graceful shutdown path. The SIGINT handler had to change readiness before the process exited while allowing the application to remain responsive during the final two-second drain window.

Using the daemon thread to control that drain period helped me understand how shutdown behavior connects to orchestration. I completed this build to learn how to implement liveness, readiness, dependency checking, tracing, and graceful shutdown in an observable Flask service. Next, I want to deploy this pattern into Kubernetes so the health signals and drain behavior can be exercised through real orchestration.

---

*Built with [NextWork](https://nextwork.ai) - [View this project](https://nextwork.ai/projects/5feb991f-f1c2-43a8-b28f-eae00c0e087b)*
