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
        GateCheck{{git --version, gh auth status, gh org list}}
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

The card only moves once the day-zero gate confirms the machine can talk to GitHub, and it only ships once the AI draft clears the three-item rubric. The same rails then carry a second card with no changes.

The full build write-up, with screenshots and prose as captured during the build, lives in [`documents/ship-your-first-card-through-github.md`](./documents/ship-your-first-card-through-github.md).

## Implementation

The steps below map to the diagram. For the full walkthrough with screenshots, see [`documents/ship-your-first-card-through-github.md`](./documents/ship-your-first-card-through-github.md).

1. Install Git, the GitHub CLI, and VS Code, then run the day-zero gate: `git --version`, `gh auth status`, and `gh org list` confirm the tools work and the organization is linked before any card work starts.
2. Design first: write the requirements and acceptance criteria, a MADR decision record (drafting with Ollama and Gemma 3 for an offline, zero-cost path, with a reversal trigger to a flat-rate client if the machine lacks the RAM or disk), and the three-item rubric.
3. Stand up the rails: create the private repository inside the organization and a Project board with two issues, so the process exists before the draft.
4. Draft with the AI: feed the card template and the sample problem to Ollama and Gemma 3 to produce a structured first draft.
5. Score and gate: check the draft against the rubric. It failed item three on hedged language, so rewrite the softened line into a direct honest-limit statement ("This card does not connect a single offline person") and re-score to 3 of 3.
6. Ship and close the loop: work a feature branch, commit the signature artifacts, open a pull request with closing keywords for both issues, merge to main, and tag the release.
7. Prove reuse: run the secret mission, a second card on maternal mortality verified against the latest World Health Organization data, through the same loop with no rail changes.

## Validation

- ✅ Day-zero gate passes: `git --version`, `gh auth status`, and `gh org list` each return a confirmation before the repository is used.
- ✅ The AI draft is scored against a fixed three-item rubric, not judged by feel.
- ✅ Negative case caught: the draft failed rubric item three on hedged language and was rewritten to a direct honest-limit line, then re-scored to 3 of 3 before merge.
- ✅ The card carries all three required parts: the problem stated plainly, a sourced number with its citation, and an explicit line on what the card does not fix.
- ✅ The repository lives inside the GitHub organization, not a personal account.
- ✅ Both issues are closed by the pull request through closing keywords, and a release is tagged.
- ✅ The Project board shows both issues in Done, from the pull-request linkage rather than a manual status change.
- ✅ Secret mission proves reuse: a second card on a different problem ships through the same loop with no changes to the rails.
