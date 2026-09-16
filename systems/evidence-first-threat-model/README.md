# The Diagram That Argues Back

> Inside the [Build & Brew: The AI Dojo](../../README.md) cohort · *A live build toward the high-performing solutions engineer.*

## Overview

A threat model that a product manager has to take on faith is a diagram, not evidence. This build answers the PM with a package that can be checked without the builder in the room: which design claims survived analysis, which STRIDE predictions matched the generated findings, and which residual risks still need an owner. Evidence order is treated as part of the result, because a prediction written after reading tool output measures transcription rather than judgment.

So the design went into a local Git repository first. Seven design files and a prediction set with stable identifiers were committed at `5434c85` while `out.json` and `dfd.dot` did not yet exist, and the doc is honest that a local owner can rewrite history, so the retained files and the commit id carry the claim together. The predictions include LOCAL rows for risks the tool cannot express, so absence from output is never read as safety. Only then was the pytm 1.4.0 model built, every element traced to its PM claim, and run to nine findings across the ticket database and the summariser.

The ownership gap is the lesson. The threat register was deliberately not written before the first validation, so `check.py` failed with exit code 1 on findings with no owned decision and predictions with no ASK row. That must-fail proof shows exposure alone does not create accountability. Once every finding had an allowed decision and a named owner, High and Very High items carried explicit PM acceptance, and unmatched predictions got owned ASK rows, the second run passed all six checks and reported **recall 7 of 9** and an **unmatched rate of 3 of 10**: the tool found two threats the review missed, and the review kept three the tool cannot say. A claim-flip in an isolated run, `isThirdParty` from True to False, removed exactly one finding, LLM03, proving it hangs on PM-5 and nothing else.

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

    PM[/Product manager whose claims shape the system/]
    Reviewer[/Reviewer who must trace every statement without rerunning the workflow/]

    subgraph Commitment["Evidence order as part of the result"]
        Rule{{Predictions written after tool output measure transcription, not judgment}}
        NotProof{{Git order supports the sequence; a local owner can rewrite it, so files and commit ids carry the claim together}}
    end

    subgraph Repo["Local, auditable evidence repository"]
        Init(Local Git repository, no remote, first commit before any design file)
        Venv[(Project virtual environment: Python 3.13.13, pytm 1.4.0 imports with exit code 0)]
        Identity[(Repository-local user.name and user.email)]
        Local{{Kept local so no remote service can reorder or pollute the evidence}}
    end

    subgraph Claims["Committed before any tool output"]
        Seven[(Seven design files: requirements, architecture, trust boundaries, flows, PM claims)]
        Predictions[(STRIDE predictions with stable identifiers: YES rows and LOCAL rows)]
        LocalRows{{LOCAL rows keep risks the tool cannot express, so absence from output is never proof of safety}}
        Commit[("Commit 5434c85 records the design state while out.json and dfd.dot are still absent")]
    end

    subgraph Model["Claim-traced pytm model"]
        Pytm(pytm 1.4.0 model: actors, processes, stores, flows, boundaries, claim-linked properties)
        Traced{{Every element traces to a documented PM claim, so a finding points back to the statement that caused it}}
        OutJson[(out.json and dfd.dot generated)]
        Nine[(Nine findings: four on Ticket Database, five on Ticket Summariser)]
    end

    subgraph Gap["The ownership gap, preserved on purpose"]
        NoRegister[(threat-register.md deliberately not yet created)]
        Check(check.py, six checks, exit 1 on any failure)
        Fail1[(Run 1 fails: check 3 finds findings with no owned decision, check 6 finds unmatched predictions with no ASK row)]
        MustFail{{The required must-fail proof: exposure alone does not create accountability}}
        Separate{{Prediction quality and ownership are separate controls}}
    end

    subgraph Owned["Findings turned into owned decisions"]
        Register[(Register: every finding gets an allowed decision and a named owner)]
        PmAccept{{High and Very High findings also require explicit PM acceptance}}
        AskRows[(Unmatched predictions get owned ASK rows)]
        Pass[(Run 2 passes all six checks, exit 0)]
    end

    subgraph Measured["Recall and the unmatched rate"]
        Recall[(Recall 7 of 9, 77.8 percent: seven findings were predicted, two were not)]
        Unmatched[(Unmatched 3 of 10, 30.0 percent: three predictions the tool never expressed)]
        BothWays{{The tool found what the review missed; the review kept what the tool cannot say. Neither is complete alone}}
    end

    subgraph Sensitivity["One claim flipped, in isolation"]
        Flip(summariser.isThirdParty set from True to False in a separate run)
        Removed[(One finding fewer, none added: Ticket Summariser, LLM03, High)]
        Depends{{LLM03 depends on PM-5, the third-party claim, and disappears when it changes}}
        Preserved{{Original out.json untouched; the comparison stored separately so the graded baseline is never overwritten}}
        Narrow{{Proves this finding is sensitive to that claim, not that first-party processing removes the risk}}
    end

    subgraph Readout["Self-contained offline readout"]
        Html[(Every statement traces to a design file, a finding, a register row, a check, or the comparison)]
    end

    PM -- "states claims that set" --> Rule
    Rule -- "bounded by" --> NotProof
    Rule -- "enforced by" --> Init
    Init -- "hosts" --> Venv
    Init -- "configured with" --> Identity
    Init -- "chosen under" --> Local
    PM -- "claims documented in" --> Seven
    Seven -- "paired with" --> Predictions
    Predictions -- "include" --> LocalRows
    Seven -- "sealed by" --> Commit
    Predictions -- "sealed by" --> Commit
    Commit -- "precedes" --> Pytm
    Seven -- "converted into" --> Pytm
    Pytm -- "built under" --> Traced
    Pytm -- "emits" --> OutJson
    OutJson -- "contains" --> Nine
    Nine -- "validated before" --> NoRegister
    NoRegister -- "is why" --> Check
    Check -- "returned" --> Fail1
    Fail1 -- "is" --> MustFail
    Fail1 -- "shows" --> Separate
    Fail1 -- "corrected by writing" --> Register
    Register -- "requires" --> PmAccept
    Register -- "paired with" --> AskRows
    Register -- "re-checked, giving" --> Pass
    AskRows -- "re-checked, giving" --> Pass
    Pass -- "also reports" --> Recall
    Pass -- "also reports" --> Unmatched
    Recall -- "read beside the unmatched rate shows" --> BothWays
    LocalRows -- "are what become" --> AskRows
    Pytm -- "rerun as" --> Flip
    Flip -- "produced" --> Removed
    Removed -- "shows" --> Depends
    Flip -- "held under" --> Preserved
    Depends -- "bounded by" --> Narrow
    Pass -- "assembled into" --> Html
    BothWays -- "assembled into" --> Html
    Depends -- "assembled into" --> Html
    Html -- "answers" --> PM
    Html -- "is inspectable by" --> Reviewer
    Commit -- "is what lets sequence be checked by" --> Reviewer

    class Venv,Identity,Seven,Predictions,Commit,OutJson,Nine,NoRegister,Fail1,Register,AskRows,Pass,Recall,Unmatched,Removed,Html datastore
    class Init,Pytm,Check,Flip service
    class Rule,NotProof,Local,LocalRows,Traced,MustFail,Separate,PmAccept,BothWays,Depends,Preserved,Narrow event
    class PM,Reviewer io
```

The diagram shows the topology and data flow of the system as built. The full architectural narrative, with screenshots and prose, lives in [`documents/evidence-first-threat-model.md`](./documents/evidence-first-threat-model.md).

## Implementation

This system is built across **6 phases**:

1. Committing to an Evidence-First Threat Model
2. Preparing a Local, Auditable Evidence Repository
3. Committing PM Claims Before Tool Output
4. Building the Threat Model and Preserving the Ownership Gap
5. Turning Findings into Owned Decisions
6. Testing Claim Sensitivity and Shipping the Readout

For the full walkthrough with screenshots and step-by-step content, see [`documents/evidence-first-threat-model.md`](./documents/evidence-first-threat-model.md).

## Validation

Each build phase below is documented in [`documents/evidence-first-threat-model.md`](./documents/evidence-first-threat-model.md), with screenshots, configuration, and notes as captured during the build:

- ✅ Committing to an Evidence-First Threat Model
- ✅ Preparing a Local, Auditable Evidence Repository
- ✅ Committing PM Claims Before Tool Output
- ✅ Building the Threat Model and Preserving the Ownership Gap
- ✅ Turning Findings into Owned Decisions
- ✅ Testing Claim Sensitivity and Shipping the Readout
