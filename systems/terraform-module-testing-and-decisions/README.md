# Terraform Module Testing and Decisions

> Inside the [Build & Brew: The AI Dojo](../../README.md) cohort · *A live build toward the high-performing solutions engineer.*

## Overview

A network module that works is not enough; this build makes one that can be defended. It starts from a Terraform network module with a deliberately seeded bug: `vpc_cidr` is declared as `type = string` with no validation, so a malformed value like `not-a-cidr` passes the module boundary and fails late inside the provider. Checkov does not catch it either, because the gap lives at the input boundary rather than inside a resource policy. The whole build is organized to prove the module fails closed, not that it merely runs.

Design comes before code: a requirements brief, four Architecture Decision Records (each with a Decision, an Alternative, a Tradeoff, and a Reversal Trigger), a diagram, and a plan. An AI-drafted test matrix of five run blocks against `mock_provider` is scored against an answer key, under the hard requirement that at least one test must fail for the existing defect before any fix. Case 5 proves the absence of a boundary guard; adding a `validation` block turns it green with the test file left byte-identical, so the change is provably the module and not the assertion.

Primary role: software and platform engineering. A GitHub Actions gate runs `terraform test` and `checkov` on every push and pull request, with `mock_provider` so it needs no cloud account, no stored credentials, and no metered AI step. The build extends the same fail-closed pattern to an `availability_zones` cap of 6 with an explicit reversal condition, and it records what it deliberately did not prove: real idempotence, left unclaimed because mocks are deterministic but not stateful.

## Architecture

```mermaid
---
title: Terraform Module Testing and Decisions
---
%%{init: {"theme":"base","themeVariables": {"primaryColor":"#1B4332","primaryTextColor":"#F4D03F","primaryBorderColor":"#F4D03F","secondaryColor":"#264653","tertiaryColor":"#2F5233","lineColor":"#F4D03F","fontFamily":"ui-monospace, SFMono-Regular, Menlo, Consolas, monospace","fontSize":"13px"}}}%%
flowchart TD
    classDef datastore fill:#264653,stroke:#F4D03F,stroke-width:2px,color:#FFFFFF
    classDef service fill:#1B4332,stroke:#F4D03F,stroke-width:2px,color:#F4D03F
    classDef event fill:#7B42BC,stroke:#F4D03F,stroke-width:2px,color:#FFFFFF
    classDef io fill:#0d1117,stroke:#F4D03F,stroke-width:1.5px,color:#F4D03F,font-style:italic

    Engineer[/Engineer proving a network module/]
    GreenProof[/Red to green, tests pass, scan clean, gate green/]

    subgraph Setup["Toolchain and rails"]
        Toolchain[(Terraform, Checkov, gh)]
        Repo[(GitHub repo and board)]
        SeededBug{{Seeded bug: vpc_cidr type=string, no validation}}
    end

    subgraph Design["Design first"]
        Requirements[(Requirements brief)]
        ADRs[(4 ADRs: Decision, Alternative, Tradeoff, Reversal Trigger)]
        Plan[(Diagram and build plan)]
    end

    subgraph Module["Network module"]
        Variables[(variables.tf)]
        CidrSubnet(cidrsubnet calculation)
        BoundaryGap{{Malformed CIDR passes the boundary, fails late}}
    end

    subgraph TestMatrix["Test matrix, mock_provider"]
        AnswerKey[(Answer key)]
        AIDraft(AI drafts 5 run blocks)
        ScoreTests{{Scored: at least one must fail for the defect}}
        Case5{{Case 5: malformed CIDR must be rejected}}
    end

    subgraph Fix["Fix and prove red to green"]
        ValidationBlock(Add vpc_cidr validation, fail-closed)
        RedGreen{{story/2 red to story/3 green, test file byte-identical}}
        CheckovScan(checkov: 4 findings decided or skipped with a reason)
    end

    subgraph Gate["CI gate"]
        GHActions(GitHub Actions: terraform test and checkov)
        NoCloud{{mock_provider: no cloud account, no metered AI}}
        GateProof{{Red and green commit SHAs}}
    end

    subgraph Extend["Extend and reverse"]
        AZCap(availability_zones validation, cap 6)
        Case6{{Case 6: a 7-element list is rejected}}
        Reversal[/Reverse if a region exceeds 6 AZs/]
    end

    Engineer -- "verifies toolchain" --> Toolchain
    Toolchain -- "controlled path" --> Repo
    Repo -- "exposes" --> SeededBug
    SeededBug -- "lives in" --> Variables
    Requirements -- "records intent" --> ADRs
    ADRs -- "drawn as" --> Plan
    Plan -- "guides the fix on" --> Variables
    Variables -- "feeds" --> CidrSubnet
    Variables -- "no guard at the boundary" --> BoundaryGap
    AnswerKey -- "checks" --> AIDraft
    AIDraft -- "writes network.tftest.hcl" --> ScoreTests
    ScoreTests -- "includes" --> Case5
    Case5 -- "proves absence against" --> BoundaryGap
    Case5 -- "fails red" --> RedGreen
    ValidationBlock -- "moves rejection to assignment" --> Variables
    ValidationBlock -- "turns Case 5 green" --> RedGreen
    CheckovScan -- "clean or justified" --> GHActions
    RedGreen -- "runs on push and PR" --> GHActions
    GHActions -- "no credentials" --> NoCloud
    GHActions -- "recorded as" --> GateProof
    GateProof -- "catches the defect class" --> GreenProof
    ValidationBlock -- "same pattern" --> AZCap
    AZCap -- "proven by" --> Case6
    AZCap -- "explicit condition" --> Reversal
    Case6 -- "fail-closed" --> GreenProof
    Reversal -- "keeps the decision defensible" --> GreenProof

    class Toolchain,Repo,Requirements,ADRs,Plan,Variables,AnswerKey datastore
    class CidrSubnet,AIDraft,ValidationBlock,CheckovScan,GHActions,AZCap service
    class SeededBug,BoundaryGap,ScoreTests,Case5,RedGreen,NoCloud,GateProof,Case6 event
    class Engineer,GreenProof,Reversal io
```

The diagram shows the topology and data flow of the system as built. The full architectural narrative, with screenshots and prose, lives in [`documents/terraform-module-testing-and-decisions.md`](./documents/terraform-module-testing-and-decisions.md).

## Implementation

This system is built across **6 phases**:

1. Setting Up the Development Environment and Identifying the Seeded Bug
2. Designing Before Building: Requirements, ADRs, Diagram, and Plan
3. Drafting the Test Matrix with AI and Scoring the Results
4. Fixing the Module, Passing All Tests, and Scanning Clean
5. Proving the CI Gate, Defending Every Decision, and Closing the Board
6. Extending the Module: Additional Validation and Reversal Triggers

For the full walkthrough with screenshots and step-by-step content, see [`documents/terraform-module-testing-and-decisions.md`](./documents/terraform-module-testing-and-decisions.md).

## Validation

Each build phase below is documented in [`documents/terraform-module-testing-and-decisions.md`](./documents/terraform-module-testing-and-decisions.md), with screenshots, configuration, and notes as captured during the build:

- ✅ Setting Up the Development Environment and Identifying the Seeded Bug
- ✅ Designing Before Building: Requirements, ADRs, Diagram, and Plan
- ✅ Drafting the Test Matrix with AI and Scoring the Results
- ✅ Fixing the Module, Passing All Tests, and Scanning Clean
- ✅ Proving the CI Gate, Defending Every Decision, and Closing the Board
- ✅ Extending the Module: Additional Validation and Reversal Triggers
