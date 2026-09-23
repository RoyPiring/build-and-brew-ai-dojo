<img src="https://cdn.prod.website-files.com/677c400686e724409a5a7409/6790ad949cf622dc8dcd9fe4_nextwork-logo-leather.svg" alt="NextWork" width="300" />

# Build an Architecture Decision Log

**Project Link:** [View Project](https://nextwork.ai/projects/5b93c334-7657-4697-8a7e-dfc4815ed08c)

**Author:** Roy Piring: Cloud Platform Engineer | Build Master  
**Email:** rpiringhawaii@gmail.com

---

![Image](https://nextwork.ai/refreshed_maroon_timid_jujube/uploads/5b93c334-7657-4697-8a7e-dfc4815ed08c_w7ya43wv)

## Framing an Evidence-Led Decision Log

### Project scope and protected build time

In this project, I'm building a local Architecture Decision Log to maintain a clean, auditable history of technical choices while protecting project scope by establishing a strict, version-controlled baseline that proves the inventory existed before any decision records were created.

## Establishing a Traceable Local Repository

### Preparing the local Git foundation

In this step, I'm setting up the core workload evidence and the initial decision inventory so that I can establish a verifiable baseline before creating any architecture decision records.

![Image](https://nextwork.ai/refreshed_maroon_timid_jujube/uploads/5b93c334-7657-4697-8a7e-dfc4815ed08c_f5kfcqim)

## Committing the Evidence Baseline First

### Copying the workload and design evidence

In this step, I'm copying the two supplied workload files and five design documents exactly as they are so that I can establish an unadulterated reading baseline. By committing this inventory before creating any Architecture Decision Records, I ensure that the coverage denominator remains fixed and verifiable in the Git history.

![Image](https://nextwork.ai/refreshed_maroon_timid_jujube/uploads/5b93c334-7657-4697-8a7e-dfc4815ed08c_n0162r2h)

## Discovering Decisions with a Coverage Checker

### Building the decision discovery checker

In this step, I'm committing the Python checker script and executing it to generate V3 evidence so that I can expose five additional configuration decisions and expand my decision inventory from four to nine items without modifying the Terraform files.

![Image](https://nextwork.ai/refreshed_maroon_timid_jujube/uploads/5b93c334-7657-4697-8a7e-dfc4815ed08c_by3p7jsp)

### Expanding the decision coverage denominator

Run 1 scanned workload/main.tf and found six settings. Five of them were absent from the four note rows: image, instance_type, engine_version, backup_retention_period, and multi_az.

Those five rows were added to inventory.md. The decision denominator changed from 4 decisions found to 9.

## Recording Decisions and Preserving Open Questions

### Creating ADRs and an uncovered-decision backlog

In this step, I'm recording three note-backed architectural decisions so that I can establish a clear, evidence-based history for the project's infrastructure choices, separating documented facts from open questions for future maintainers.

### Separating recorded evidence from unknown rationale

A RECORDED driver ties a fact to a source, so the next owner can check the claim instead of trusting a retelling.

A NOT RECORDED driver keeps the missing reason as a question, so nobody fills the gap with an invented rationale.

The handover stays honest: known facts stay traced, and unknown reasons stay visible.

![Image](https://nextwork.ai/refreshed_maroon_timid_jujube/uploads/5b93c334-7657-4697-8a7e-dfc4815ed08c_sojl10lb)

## Proving the Checks and Inventory History

### Retaining checker evidence and Git proof

In this step, I'm validating the checker's error reporting and proving the historical integrity of my Git repository so that I can ensure the documentation process is both fail-closed and auditable.

![Image](https://nextwork.ai/refreshed_maroon_timid_jujube/uploads/5b93c334-7657-4697-8a7e-dfc4815ed08c_gdsnmloy)

### What the intentional failures demonstrate

Run 2 proves the checker rejects a record that is missing a required heading. It named the missing ## Consequences heading and exited 1.

Run 3 proves the checker rejects a supersession that points only one way. It named the broken link between ADR-0004 and ADR-0005 and exited 1.

Both failures were kept as evidence, and the records were restored before the clean pass.

## Auditing the Final Product-Manager Readout

### Tracing first-draft claims to a shipped zero-U result

The first draft scored 3/28. Three material claims had no source in a supplied file: the untraced "ready to run" line, the artifact claim with no file trace, and the self-scored 0/27 line.

Those unsupported claims were removed when the approved audit replaced the draft. The shipped score is 0/owed, because every remaining claim is either traced or marked owed.

## Reflecting on Evidence-Led Architecture Handover

### Tools and concepts applied

The key tools I used include Python's standard library for building the decision checker, Git for maintaining a verifiable evidence trail and commit history, and a local text editor to manage the decision records and handover documentation.

Key concepts I learnt include the importance of establishing a "decision inventory" that predates any architecture decision records (ADRs) to ensure traceability, the use of a coverage-based checker to identify unanswered questions, and the technique of performing a "secret mission" audit to ensure all shipped claims in a handover are fully supported by internal evidence without relying on unverified external assertions.

### Build time and key challenge

This project took me approximately 60 minutes. The most challenging part was ensuring the evidence trail accurately reflected the chronological order of the inventory and ADRs while auditing the AI-generated content to reach a zero-untraceable-claim status.

### Next learning goal

I completed this project today to learn how to create a durable, evidence-led architecture decision log that maintains a clear audit trail between technical choices and their underlying documentation. Another skill I want to learn is how to automate the validation of these decision records within a CI/CD pipeline to ensure documentation remains synchronized with infrastructure changes.

---

*Built with [NextWork](https://nextwork.ai) - [View this project](https://nextwork.ai/projects/5b93c334-7657-4697-8a7e-dfc4815ed08c)*
