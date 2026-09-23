# Build an Architecture Decision Log

> Inside the [Build & Brew: The AI Dojo](../../README.md) cohort · *A live build toward the high-performing solutions engineer.*

## Overview

A decision log written after the fact measures memory, not decisions. This build keeps a local architecture decision log whose credibility rests on order: the workload evidence and the decision inventory are committed before any record exists, so the coverage denominator is fixed in Git history and cannot be quietly trimmed to whatever the records happened to cover. Two supplied workload files and five design documents were copied exactly as given, with no edits, so the reading baseline is the supplied material rather than a retelling of it.

The denominator then grew from evidence rather than opinion. A Python checker, written against the standard library and committed before it was run, scanned `workload/main.tf` and found six settings. Five of them appeared nowhere in the four note rows: `image`, `instance_type`, `engine_version`, `backup_retention_period`, and `multi_az`. Those rows went into the inventory and the decision count moved from four to nine, with the Terraform files never modified, so discovery could not change the thing being measured. Three note-backed records were written against that larger denominator, and the rest stayed visible as an uncovered-decision backlog rather than disappearing.

The record format keeps two kinds of statement apart. A RECORDED driver ties a fact to a source, so the next owner can check the claim instead of trusting a retelling. A NOT RECORDED driver keeps the missing reason as a question, so nobody fills the gap with an invented rationale. Known facts stay traced and unknown reasons stay visible, which is what makes the handover honest rather than tidy.

The checker was then proven to fail. Run 2 rejected a record missing its `## Consequences` heading and exited 1; run 3 rejected a supersession that pointed only one way, naming the broken link between ADR-0004 and ADR-0005, and exited 1. Both failing runs were kept as evidence and the records restored before the clean pass, so the pass means something. The same standard was turned on the handover itself: the first readout draft scored 3 of 28 traced claims, and three material claims had no source in any supplied file, including a ready-to-run line, an artifact claim with no file trace, and the draft's own self-scored 0 of 27. A self-scored line is not evidence, so it went with the others. The shipped readout scores 0 owed, because every claim left on the page is either traced to a file or marked as owed.

## Architecture

```mermaid
---
title: Build an Architecture Decision Log
---
%%{init: {"theme":"base","themeVariables": {"primaryColor":"#1B4332","primaryTextColor":"#F4D03F","primaryBorderColor":"#F4D03F","secondaryColor":"#264653","tertiaryColor":"#2F5233","lineColor":"#F4D03F","fontFamily":"ui-monospace, SFMono-Regular, Menlo, Consolas, monospace","fontSize":"13px"}}}%%
flowchart TD
    classDef datastore fill:#264653,stroke:#F4D03F,stroke-width:2px,color:#FFFFFF
    classDef service fill:#1B4332,stroke:#F4D03F,stroke-width:2px,color:#F4D03F
    classDef event fill:#7B42BC,stroke:#F4D03F,stroke-width:2px,color:#FFFFFF
    classDef io fill:#0d1117,stroke:#F4D03F,stroke-width:1.5px,color:#F4D03F,font-style:italic

    NextOwner[/The next owner, who must check a claim rather than trust a retelling/]
    PM[/Product manager reading the handover readout/]

    subgraph Scope["Scope fixed before any record exists"]
        Intent{{A clean, auditable history of technical choices, with the inventory proven to predate every decision record}}
        Protected{{Scope protected by a strict version-controlled baseline rather than by discipline alone}}
    end

    subgraph Baseline["The evidence baseline, committed first"]
        Repo(Local Git repository prepared before any architecture decision record)
        Workload[(Two supplied workload files copied exactly as given)]
        Design[(Five design documents copied exactly as given)]
        Inventory[(Initial decision inventory: four note rows)]
        Commit[("Baseline commit: the coverage denominator becomes fixed and verifiable in Git history")]
        Unadulterated{{Copied without edits, so the reading baseline is what was supplied and not a retelling of it}}
    end

    subgraph Checker["Coverage checker, written in the standard library"]
        Script(Python checker script, committed before it was run)
        Run1[(Run 1 scans workload/main.tf and finds six settings)]
        Missing[(Five absent from the notes: image, instance_type, engine_version, backup_retention_period, multi_az)]
        Grew[(Inventory rows 4 to 9: the denominator grew from evidence, not from opinion)]
        NoEdit{{The Terraform files were never modified, so discovery could not quietly change the thing being measured}}
    end

    subgraph Records["Decisions recorded, open questions kept open"]
        Adrs[(Three note-backed architecture decision records)]
        Recorded[(RECORDED driver: the fact is tied to a source the next owner can check)]
        NotRecorded[(NOT RECORDED driver: the missing reason is kept as a question)]
        Backlog[(Uncovered-decision backlog for the rows with no record yet)]
        Honest{{Known facts stay traced and unknown reasons stay visible, so nobody fills a gap with an invented rationale}}
    end

    subgraph MustFail["Failures kept as evidence"]
        Run2[(Run 2 rejects a record missing the Consequences heading and exits 1)]
        Run3[(Run 3 rejects a supersession that points only one way, naming the broken link between ADR-0004 and ADR-0005, and exits 1)]
        Named{{Each failure names what is wrong, so the message is usable without reading the checker}}
        Kept[(Both failing runs retained, then the records restored before the clean pass)]
        Proof{{A clean pass means something only because the failing cases were shown first}}
    end

    subgraph Audit["The readout audited against its own evidence"]
        Draft[(First draft scores 3 of 28 traced claims)]
        Untraced[(Three material claims with no source in a supplied file: a ready-to-run line, an artifact claim with no file trace, a self-scored 0 of 27 line)]
        Removed(Unsupported claims removed when the approved audit replaced the draft)
        Shipped[(Shipped score 0 owed: every remaining claim is traced or marked owed)]
        NotSelf{{A self-scored line is not evidence, which is why the draft's own score was one of the claims cut}}
    end

    subgraph Limits["What the log does not claim"]
        Local{{The history is local, so ordering is inspectable rather than tamper-proof}}
        Owed{{An owed claim is an open question with a name on it, not a resolved one}}
        Next{{Validation still runs by hand; moving it into CI is the stated next step}}
    end

    NextOwner -- "needs" --> Intent
    Intent -- "held by" --> Protected
    Protected -- "realised as" --> Repo
    Repo -- "receives" --> Workload
    Repo -- "receives" --> Design
    Workload -- "read into" --> Inventory
    Design -- "read into" --> Inventory
    Workload -- "copied under" --> Unadulterated
    Design -- "copied under" --> Unadulterated
    Inventory -- "sealed by" --> Commit
    Commit -- "precedes" --> Script
    Script -- "executed as" --> Run1
    Run1 -- "compared against the four note rows, exposing" --> Missing
    Missing -- "added back, giving" --> Grew
    Run1 -- "bounded by" --> NoEdit
    Grew -- "is the denominator for" --> Adrs
    Adrs -- "each driver is either" --> Recorded
    Adrs -- "or" --> NotRecorded
    Recorded -- "read by" --> NextOwner
    NotRecorded -- "preserved in" --> Backlog
    NotRecorded -- "keeps the handover" --> Honest
    Adrs -- "checked by" --> Run2
    Adrs -- "checked by" --> Run3
    Run2 -- "reports through" --> Named
    Run3 -- "reports through" --> Named
    Run2 -- "committed as" --> Kept
    Run3 -- "committed as" --> Kept
    Kept -- "is what makes" --> Proof
    Adrs -- "summarised in" --> Draft
    Draft -- "audited, exposing" --> Untraced
    Untraced -- "resolved by" --> Removed
    Removed -- "produces" --> Shipped
    Untraced -- "includes the reason for" --> NotSelf
    Shipped -- "handed to" --> PM
    Shipped -- "carries forward" --> Owed
    Commit -- "bounded by" --> Local
    Proof -- "bounded by" --> Next
    Backlog -- "inspectable by" --> NextOwner

    class Workload,Design,Inventory,Commit,Run1,Missing,Grew,Adrs,Recorded,NotRecorded,Backlog,Run2,Run3,Kept,Draft,Untraced,Shipped datastore
    class Repo,Script,Removed service
    class Intent,Protected,Unadulterated,NoEdit,Honest,Named,Proof,NotSelf,Local,Owed,Next event
    class NextOwner,PM io
```

The diagram shows the topology and data flow of the system as built. The full architectural narrative, with screenshots and prose, lives in [`documents/coverage-checked-decision-log.md`](./documents/coverage-checked-decision-log.md).

## Implementation

This system is built across **7 phases**:

1. Framing an Evidence-Led Decision Log
2. Establishing a Traceable Local Repository
3. Committing the Evidence Baseline First
4. Discovering Decisions with a Coverage Checker
5. Recording Decisions and Preserving Open Questions
6. Proving the Checks and Inventory History
7. Auditing the Final Product-Manager Readout

For the full walkthrough with screenshots and step-by-step content, see [`documents/coverage-checked-decision-log.md`](./documents/coverage-checked-decision-log.md).

## Validation

Each build phase below is documented in [`documents/coverage-checked-decision-log.md`](./documents/coverage-checked-decision-log.md), with screenshots, configuration, and notes as captured during the build:

- ✅ Framing an Evidence-Led Decision Log
- ✅ Establishing a Traceable Local Repository
- ✅ Committing the Evidence Baseline First
- ✅ Discovering Decisions with a Coverage Checker
- ✅ Recording Decisions and Preserving Open Questions
- ✅ Proving the Checks and Inventory History
- ✅ Auditing the Final Product-Manager Readout
