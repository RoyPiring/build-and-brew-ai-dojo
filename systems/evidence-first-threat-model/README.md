# The Diagram That Argues Back

> Inside the [Build & Brew: The AI Dojo](../../README.md) cohort · *A live build toward the high-performing solutions engineer.*

## Overview

A threat model that a product manager has to take on faith is a diagram, not evidence. This build is an investigation arranged so the findings can argue with the design without rewriting what was expected of it: a local Git repository first, the product manager's design frozen with twelve upfront STRIDE predictions second, and only after that commit is pytm 1.4.0 allowed to generate a finding or a data-flow diagram. A pre-run verification recorded the boundary as data: seven design files and the prediction set present, `out.json` and `dfd.dot` absent. The doc is honest that a local owner can rewrite history, and names what stronger preservation would take: signed commits, a protected remote, or an independent timestamp service.

The model translated the AI ticket summariser's actors, processes, stores, flows, trust boundaries, and claim-sensitive properties into Python, with every property traced back to a written claim, and produced 126 findings. The validator was then run with `threat-register.md` deliberately absent, and it failed, because findings without named owners and allowed decisions are detection, not accountability. That failure was committed before the register existed, so the later pass came from work rather than from a validator that always says yes.

The register gave every finding a decision and an owner, required explicit PM acceptance on High and Very High items, and turned unmatched predictions into classified, owned ASK rows rather than deleting them to raise the score. The second run passed all six checks with `EXIT: 0` and reported **recall of 7 of 9** and an **unmatched rate of 3 of 10**: pytm found two threats the review missed, and the review kept three the tool did not report. A claim-flip in an isolated run, `summariser.isThirdParty` from True to False, took the output from 126 findings to 125, removing exactly one, LLM03 Sensitive Data Leakage to Third-Party Provider, and adding none. The whole chain ships as an offline HTML evidence pack a reviewer can inspect without rerunning the build.

## Architecture

```mermaid
---
title: The Diagram That Argues Back
---
%%{init: {"theme":"base","themeVariables": {"primaryColor":"#1B4332","primaryTextColor":"#F4D03F","primaryBorderColor":"#F4D03F","secondaryColor":"#264653","tertiaryColor":"#2F5233","lineColor":"#F4D03F","fontFamily":"ui-monospace, SFMono-Regular, Menlo, Consolas, monospace","fontSize":"13px"}}}%%
flowchart TD
    classDef datastore fill:#264653,stroke:#F4D03F,stroke-width:2px,color:#FFFFFF
    classDef service fill:#1B4332,stroke:#F4D03F,stroke-width:2px,color:#F4D03F
    classDef event fill:#7B42BC,stroke:#F4D03F,stroke-width:2px,color:#FFFFFF
    classDef io fill:#0d1117,stroke:#F4D03F,stroke-width:1.5px,color:#F4D03F,font-style:italic

    PM[/Product manager whose design claims the findings must challenge/]
    Reviewer[/Reviewer who inspects the evidence pack without rerunning the build/]

    subgraph Framing["Evidence order protected before anything runs"]
        Order{{Design frozen with twelve upfront STRIDE predictions before pytm may generate a finding or a diagram}}
        NotImmutable{{Git history is not immutable, but every change is inspectable}}
    end

    subgraph Repo["Timestamped evidence repository"]
        Init(Local Git repository, no remote, repository-specific author details)
        Ignore[(Ignore rules for temporary files and the virtual environment)]
        Venv[(Virtual environment with pytm 1.4.0, import validated before the model was written)]
        Stages[(Commits split by stage: setup, design, predictions, outputs, validator failure, ownership, reporting)]
        Stronger{{Stronger preservation would need signed commits, a protected remote, or an independent timestamp}}
    end

    subgraph Claims["Predictions documented before results"]
        Seven[(Seven design files: claims, components, trust boundaries, data flows, assumptions)]
        Predictions[(STRIDE predictions with stable identifiers, plus local rows for concerns pytm might not generate)]
        PreRun[("Pre-run verification: seven design files and predictions True, out.json and dfd.dot False")]
        DesignCommit[(Design and prediction set committed with that result)]
        Boundary{{A file-order boundary between human expectation and tool output; hindsight cannot be mistaken for judgment}}
    end

    subgraph Model["The model and its required first failure"]
        Pytm(pytm model: actors, processes, stores, flows, trust boundaries, claim-sensitive properties)
        Outputs[(dfd.dot and out.json, 126 findings, each property traced to a written claim)]
        NoRegister[(threat-register.md deliberately absent)]
        Check(check.py, six checks, non-zero status on any failure)
        Fail1[(Run 1 fails: findings with no owner or decision, unmatched predictions with no ASK row)]
        MustFail{{Detection creates evidence of risk; the register connects each result to a decision-maker}}
    end

    subgraph Owned["Findings validated and prediction order proved"]
        Register[(Register: a row per finding with an allowed decision and a named owner)]
        PmAccept{{High and Very High findings require explicit PM acceptance}}
        AskRows[(Unmatched predictions become classified, owned ASK rows)]
        Pass[(Run 2 passes all six checks, EXIT: 0)]
        Recall[(Recall 7 of 9, 77.8 percent: two scored findings were not anticipated)]
        Unmatched[(Unmatched 3 of 10, 30.0 percent: three predictions kept rather than deleted)]
        BothWays{{Disagreement in both directions: neither the review nor the tool is the sole authority}}
    end

    subgraph Sensitivity["One third-party claim flipped, in isolation"]
        Flip(summariser.isThirdParty set from True to False in a separate run)
        Flipped[(out-flipped.json: 125 findings, one removed, none added, everything else identical)]
        Removed[(Removed: Ticket Summariser, LLM03, High, Sensitive Data Leakage to Third-Party Provider)]
        Preserved{{Original out.json kept; the flipped result stored separately so graded evidence is never rewritten}}
        Narrow{{Proves that finding traces to the third-party claim, not that first-party processing makes the summariser safe}}
    end

    subgraph Readout["Offline HTML evidence pack"]
        Pack[(Design, predictions, findings, ownership decisions, and the claim-flip comparison in one inspectable package)]
    end

    PM -- "states the design that sets" --> Order
    Order -- "bounded by" --> NotImmutable
    Order -- "enforced by" --> Init
    Init -- "carries" --> Ignore
    Init -- "hosts" --> Venv
    Init -- "records" --> Stages
    Stages -- "bounded by" --> Stronger
    PM -- "claims documented in" --> Seven
    Seven -- "paired with" --> Predictions
    Seven -- "checked by" --> PreRun
    Predictions -- "checked by" --> PreRun
    PreRun -- "preserved in" --> DesignCommit
    DesignCommit -- "establishes" --> Boundary
    Seven -- "translated into" --> Pytm
    DesignCommit -- "precedes" --> Pytm
    Pytm -- "generates" --> Outputs
    Outputs -- "validated while" --> NoRegister
    NoRegister -- "is why" --> Check
    Check -- "returned" --> Fail1
    Fail1 -- "committed as proof that" --> MustFail
    Fail1 -- "corrected by writing" --> Register
    Register -- "requires" --> PmAccept
    Register -- "paired with" --> AskRows
    Predictions -- "unmatched rows become" --> AskRows
    Register -- "re-checked, giving" --> Pass
    AskRows -- "re-checked, giving" --> Pass
    Pass -- "reports" --> Recall
    Pass -- "reports" --> Unmatched
    Recall -- "read beside the unmatched rate shows" --> BothWays
    Pytm -- "rerun as" --> Flip
    Flip -- "produced" --> Flipped
    Flipped -- "differs only by" --> Removed
    Flip -- "held under" --> Preserved
    Removed -- "bounded by" --> Narrow
    Pass -- "exported into" --> Pack
    BothWays -- "exported into" --> Pack
    Removed -- "exported into" --> Pack
    Pack -- "answers" --> PM
    Pack -- "inspectable by" --> Reviewer
    DesignCommit -- "lets the sequence be checked by" --> Reviewer

    class Ignore,Venv,Stages,Seven,Predictions,PreRun,DesignCommit,Outputs,NoRegister,Fail1,Register,AskRows,Pass,Recall,Unmatched,Flipped,Removed,Pack datastore
    class Init,Pytm,Check,Flip service
    class Order,NotImmutable,Stronger,Boundary,MustFail,PmAccept,BothWays,Preserved,Narrow event
    class PM,Reviewer io
```

The diagram shows the topology and data flow of the system as built. The full architectural narrative, with screenshots and prose, lives in [`documents/evidence-first-threat-model.md`](./documents/evidence-first-threat-model.md).

## Implementation

This system is built across **6 phases**:

1. Establishing an Evidence-First Investigation
2. Creating a Timestamped Evidence Repository
3. Documenting Predictions Before Results
4. Building the Model and Capturing Its Initial Failure
5. Validating Findings and Proving Prediction Order
6. Testing How a Third-Party Claim Changes the Findings

For the full walkthrough with screenshots and step-by-step content, see [`documents/evidence-first-threat-model.md`](./documents/evidence-first-threat-model.md).

## Validation

Each build phase below is documented in [`documents/evidence-first-threat-model.md`](./documents/evidence-first-threat-model.md), with screenshots, configuration, and notes as captured during the build:

- ✅ Establishing an Evidence-First Investigation
- ✅ Creating a Timestamped Evidence Repository
- ✅ Documenting Predictions Before Results
- ✅ Building the Model and Capturing Its Initial Failure
- ✅ Validating Findings and Proving Prediction Order
- ✅ Testing How a Third-Party Claim Changes the Findings
