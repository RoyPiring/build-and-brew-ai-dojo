# Ship Your First Card Through GitHub

> Inside the [Build & Brew: The AI Dojo](../../README.md) cohort · *A live build toward the high-performing solutions engineer.*

## Overview

The card is the easy part. This build ships a sourced Global-Problem Card through a full GitHub delivery loop, and the real lesson is the rail system around it: issues, acceptance criteria, rubric checks, a reviewed pull request, and a clean tagged release. Software delivery is not only making an artifact. It is proving how the artifact moved from planned work to finished work, with a trail someone else can inspect.

This is the warm-up rep every later build reuses. The point of view is the high-performing solutions engineer, and the build drills all three cohort reflexes at once: it designs before it builds (requirements, a decision record, and a rubric come first), it proves the result with a fixed rubric rather than a feeling, and it refuses to trust the confident AI answer. The AI drafts the card; the rubric decides whether the draft is allowed to ship.

Primary role: the delivery loop, the DevOps and forward-deployed habits every domain build assumes. It is one live build, with the sample problem, the card template, and the three-item rubric staged so the session spends its attention on the loop, not on setup.

## Architecture

```mermaid
---
title: Ship a Card Through the GitHub Delivery Loop
---
%%{init: {"theme":"base","themeVariables": {"primaryColor":"#1B4332","primaryTextColor":"#F4D03F","primaryBorderColor":"#F4D03F","secondaryColor":"#264653","tertiaryColor":"#2F5233","lineColor":"#F4D03F","fontFamily":"ui-monospace, SFMono-Regular, Menlo, Consolas, monospace","fontSize":"13px"}}}%%
flowchart TD
    classDef datastore fill:#264653,stroke:#F4D03F,stroke-width:2px,color:#FFFFFF
    classDef service fill:#1B4332,stroke:#F4D03F,stroke-width:2px,color:#F4D03F
    classDef event fill:#7B42BC,stroke:#F4D03F,stroke-width:2px,color:#FFFFFF
    classDef io fill:#0d1117,stroke:#F4D03F,stroke-width:1.5px,color:#F4D03F,font-style:italic

    Learner[/Learner shipping the first rep/]
    Decision[/A shipped card with a clean planned-to-done trail/]

    subgraph Setup["Day-zero gate"]
        Toolchain(Git, gh CLI, and VS Code installed)
        GateCheck{{"git --version, gh auth status, gh org list"}}
        OrgReady[(GitHub organization linked)]
    end

    subgraph Design["Design first"]
        Requirements[(Requirements and acceptance criteria)]
        ADR[(MADR: Ollama with Gemma 3, offline and zero cost)]
        Rubric[(Three-item card rubric)]
    end

    subgraph Rails["Delivery rails in the org"]
        Repo[(Private repository)]
        Board[(Project board with two issues)]
        SampleProblem[(Sample problem file)]
        CardTemplate[(Card template)]
    end

    subgraph Draft["AI draft and the quality gate"]
        AiDraft(Ollama Gemma 3 drafts the card)
        ScoreGate{{Score the draft against the rubric}}
        HonestLimitFix(Rewrite item three to a direct honest-limit line)
        ScorePass{{3 of 3, cleared to ship}}
    end

    subgraph Ship["Ship and close the loop"]
        Branch(Feature branch, commit the signature artifacts)
        PullRequest(Pull request with closing keywords)
        Merge{{Merge to main, both issues closed}}
        Release[(Tagged release)]
        BoardDone{{Board shows both issues Done}}
    end

    subgraph Secret["Secret mission: reusable rails"]
        SecondCard(Second card: maternal mortality, WHO data)
        ReuseProof{{Same loop, no rail changes}}
    end

    Learner -- "runs" --> Toolchain
    Toolchain -- "checked by" --> GateCheck
    GateCheck -- "confirms" --> OrgReady
    OrgReady -- "before any card work" --> Requirements
    Requirements -- "sets the bar" --> Rubric
    Requirements -- "records the choice" --> ADR
    Requirements -- "opens the workspace" --> Repo
    ADR -- "local drafting path" --> AiDraft
    Repo -- "plans the work" --> Board
    SampleProblem -- "fed to" --> AiDraft
    CardTemplate -- "structures" --> AiDraft
    AiDraft -- "first artifact" --> ScoreGate
    Rubric -- "fixed standard" --> ScoreGate
    ScoreGate -- "item three failed, hedged" --> HonestLimitFix
    HonestLimitFix -- "re-scored" --> ScorePass
    ScorePass -- "committed on" --> Branch
    Board -- "issues linked to" --> PullRequest
    Branch -- "opened as" --> PullRequest
    PullRequest -- "closing keywords" --> Merge
    Merge -- "tagged as" --> Release
    Merge -- "moves issues to" --> BoardDone
    Release -- "reused by" --> SecondCard
    SecondCard -- "same rails" --> ReuseProof
    ReuseProof -- "proves the loop" --> Decision
    BoardDone -- "planned to done" --> Decision
    Release -- "stable version" --> Decision

    class OrgReady,Requirements,ADR,Rubric,Repo,Board,SampleProblem,CardTemplate,Release datastore
    class Toolchain,AiDraft,HonestLimitFix,Branch,PullRequest,SecondCard service
    class GateCheck,ScoreGate,ScorePass,Merge,BoardDone,ReuseProof event
    class Learner,Decision io
```

The diagram shows the topology and data flow of the system as built. The full architectural narrative, with screenshots and prose, lives in [`documents/ship-your-first-card-through-github.md`](./documents/ship-your-first-card-through-github.md).

## Implementation

This system is built across **6 phases**:

1. Setting Up the Engineering Environment
2. Designing Before Building
3. Standing Up the Rails and AI-Drafting the Card
4. Scoring the Draft and Enforcing the Quality Gate
5. Shipping the Card and Closing the Loop
6. Proving the Rails Are Reusable

For the full walkthrough with screenshots and step-by-step content, see [`documents/ship-your-first-card-through-github.md`](./documents/ship-your-first-card-through-github.md).

## Validation

Each build phase below is documented in [`documents/ship-your-first-card-through-github.md`](./documents/ship-your-first-card-through-github.md), with screenshots, configuration, and notes as captured during the build:

- ✅ Setting Up the Engineering Environment
- ✅ Designing Before Building
- ✅ Standing Up the Rails and AI-Drafting the Card
- ✅ Scoring the Draft and Enforcing the Quality Gate
- ✅ Shipping the Card and Closing the Loop
- ✅ Proving the Rails Are Reusable
