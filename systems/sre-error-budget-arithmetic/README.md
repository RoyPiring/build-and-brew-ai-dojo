# SRE Error Budget Arithmetic

> Inside the [Build & Brew: The AI Dojo](../../README.md) cohort · *A live build toward the high-performing solutions engineer.*

## Overview

A two-person on-call roster cannot absorb a page that did not need to happen. This build answers the question that decides it: not whether errors exist, because every service produces some, but how fast the current rate is spending the month's error budget. A 99.9% availability target allows about 43 minutes of unavailability a month, and burn rate is the arithmetic that turns that allowance into a defensible page-or-ticket call.

Every threshold is derived by hand before it is trusted. A single Sloth specification generates the multi-window, multi-burn-rate Prometheus rules, and the formula `budget_share * 720 / window` produces 14.4, 6, 3, and 1 independently. All four burn rates and all eight long/short windows matched the generated output, which is what separates a generator following the SRE Workbook method from one applying an unexplained constant. Three candidate SLIs were argued in ADRs first, and two were rejected in writing: latency under 300ms, because a fast 500 response still lands under `le="0.3"` and would count as a good event during an outage, and a variant that dropped 499 responses, because it shrank the denominator exactly when traffic conditions became difficult.

The rules are then proven as behavior rather than asserted as formulas. promtool feeds two synthetic series into the generated rules at simulated times: a 10% outage at burn rate 100 produces both a page and a ticket at the 1-hour evaluation point, and a 0.2% leak at burn rate 2 produces a ticket at 6 hours and pages nobody. The build closes with a self-contained HTML readout a reviewer opens by double-clicking, and it states its own limits plainly. No server ran, no metrics endpoint was scraped, and no real traffic entered the calculations, so this proves configuration behavior under synthetic input, not production readiness.

## Architecture

```mermaid
---
title: SRE Error Budget Arithmetic
---
%%{init: {"theme":"base","themeVariables": {"primaryColor":"#1B4332","primaryTextColor":"#F4D03F","primaryBorderColor":"#F4D03F","secondaryColor":"#264653","tertiaryColor":"#2F5233","lineColor":"#F4D03F","fontFamily":"ui-monospace, SFMono-Regular, Menlo, Consolas, monospace","fontSize":"13px"}}}%%
flowchart TD
    classDef datastore fill:#264653,stroke:#F4D03F,stroke-width:2px,color:#FFFFFF
    classDef service fill:#1B4332,stroke:#F4D03F,stroke-width:2px,color:#F4D03F
    classDef event fill:#7B42BC,stroke:#F4D03F,stroke-width:2px,color:#FFFFFF
    classDef io fill:#0d1117,stroke:#F4D03F,stroke-width:1.5px,color:#F4D03F,font-style:italic

    Roster[/Two-person on-call roster/]
    Reviewer[/Stakeholder reviewing the alert decision/]

    subgraph Toolchain["Pinned toolchain in the repo"]
        Fetch(fetch.sh and fetch.ps1)
        Sloth(Sloth v0.16.0)
        Promtool(promtool 3.13.0)
        Workbook[(reference-table.md, Workbook chapter 5)]
    end

    subgraph Design["Design before rules"]
        Candidates[(Three candidate SLIs)]
        ADRs[(Three ADRs recording the reasoning)]
        Chosen[(Selected SLI and its denominator)]
        RejectB{{Candidate B rejected: a fast 500 lands under le=0.3}}
        RejectC{{Candidate C rejected: dropping 499s shrinks the denominator}}
    end

    subgraph Objective["The objective"]
        SLO[(99.9 percent availability target)]
        Budget[(0.001 error budget, about 43 minutes a month)]
    end

    subgraph Generate["One spec, generated rules"]
        Spec[(Sloth SLO specification)]
        Rules[(Generated MWMB recording and alert rules)]
    end

    subgraph Derive["Independent hand derivation"]
        Formula(budget_share times 720 divided by window)
        Burns[(Burn rates 14.4, 6, 3, 1)]
        Windows[(Eight long and short windows)]
        Match{{All twelve values matched the generated rules}}
    end

    subgraph Prove["Synthetic proof"]
        Outage[(series-outage.txt: 10 percent outage, burn rate 100)]
        Leak[(series-slowleak.txt: 0.2 percent leak, burn rate 2)]
        Tests(promtool evaluates rules at simulated times)
        Pages{{Page and ticket at 1 hour}}
        Ticket{{Ticket at 6 hours, no page}}
    end

    subgraph Close["Readout and close-out"]
        Readout[(Self-contained HTML stakeholder readout)]
        Scope{{Scope stated: no server, no scrape, no real traffic}}
        TeachBack[(Teach-back on fast burn versus slow burn)]
        AiScore{{AI scored against the rubric}}
    end

    Roster -- "cannot absorb an unnecessary page, so demands" --> Formula
    Fetch -- "downloads and verifies" --> Sloth
    Fetch -- "downloads and verifies" --> Promtool
    Candidates -- "argued in" --> ADRs
    ADRs -- "records the rejection of" --> RejectB
    ADRs -- "records the rejection of" --> RejectC
    ADRs -- "settles on" --> Chosen
    SLO -- "implies" --> Budget
    Chosen -- "defines the SLI inside" --> Spec
    SLO -- "sets the objective in" --> Spec
    Sloth -- "generates from the spec" --> Rules
    Spec -- "is the single source for" --> Rules
    Budget -- "supplies budget share to" --> Formula
    Workbook -- "supplies the method behind" --> Formula
    Formula -- "yields" --> Burns
    Formula -- "yields" --> Windows
    Burns -- "compared against the generated rules" --> Match
    Windows -- "compared against the generated rules" --> Match
    Rules -- "checked by" --> Match
    Outage -- "fed into" --> Tests
    Leak -- "fed into" --> Tests
    Rules -- "evaluated by" --> Tests
    Promtool -- "runs" --> Tests
    Tests -- "on the outage series produce" --> Pages
    Tests -- "on the slow leak produce" --> Ticket
    Pages -- "interrupts" --> Roster
    Ticket -- "waits for working hours of" --> Roster
    Match -- "evidence in" --> Readout
    Pages -- "evidence in" --> Readout
    Ticket -- "evidence in" --> Readout
    Readout -- "bounded by" --> Scope
    Readout -- "opened without the toolchain by" --> Reviewer
    Scope -- "tells what is not proven to" --> Reviewer
    Readout -- "defended in" --> TeachBack
    TeachBack -- "closed alongside" --> AiScore

    class Workbook,Candidates,ADRs,Chosen,SLO,Budget,Spec,Rules,Burns,Windows,Outage,Leak,Readout,TeachBack datastore
    class Fetch,Sloth,Promtool,Formula,Tests service
    class RejectB,RejectC,Match,Pages,Ticket,Scope,AiScore event
    class Roster,Reviewer io
```

The diagram shows the topology and data flow of the system as built. The full architectural narrative, with screenshots and prose, lives in [`documents/sre-error-budget-arithmetic.md`](./documents/sre-error-budget-arithmetic.md).

## Implementation

This system is built across **7 phases**:

1. Proving the Rules Work: Test Results and Alert Logic
2. Generating Alert Rules from a Single SLO Spec
3. The Stakeholder Readout: A Self-Contained Proof
4. Why This Project Exists: Roster Math and the Error Budget
5. Setting Up the Toolchain
6. Designing the SLI and Arguing Every Denominator
7. Teaching Burn Rate and Scoring the AI

For the full walkthrough with screenshots and step-by-step content, see [`documents/sre-error-budget-arithmetic.md`](./documents/sre-error-budget-arithmetic.md).

## Validation

Each build phase below is documented in [`documents/sre-error-budget-arithmetic.md`](./documents/sre-error-budget-arithmetic.md), with screenshots, configuration, and notes as captured during the build:

- ✅ Proving the Rules Work: Test Results and Alert Logic
- ✅ Generating Alert Rules from a Single SLO Spec
- ✅ The Stakeholder Readout: A Self-Contained Proof
- ✅ Why This Project Exists: Roster Math and the Error Budget
- ✅ Setting Up the Toolchain
- ✅ Designing the SLI and Arguing Every Denominator
- ✅ Teaching Burn Rate and Scoring the AI
