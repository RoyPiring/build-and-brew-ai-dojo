# The Delivery Scoreboard: Four Keys From Your Own Repo

> Inside the [Build & Brew: The AI Dojo](../../README.md) cohort · *A 45-minute live build toward the high-performing solutions engineer.*

## Overview

The quarterly review just ended. The product manager keeps telling leadership the team is elite, but when someone asked what elite means, the PM had no number. The ask is plain: build a scoreboard from the team's own repo, and say straight what each number does not prove.

This build computes the four DORA keys, deployment frequency, lead time for changes, change failure rate, and time to restore, plus rework rate added in the 2024 report, from the repo's own git and history rather than a hosted product. A Python collector reads a captured 30-day fixture of the GitHub REST API responses into DuckDB, SQL computes the metrics, and matplotlib renders the scoreboard. The definitions follow the current DORA spec and its seven team archetypes, not the retired low-to-elite tiers. The AI labels each deploy incident-versus-planned so rework rate is computable from messy history, but it never sets a number, and its labels are scored against 20 hand-labelled deploys and admitted only at 18 of 20.

What it proves is narrow and honest: the team can compute its own delivery numbers from evidence it owns and state them against the current DORA model. What it does not prove ships in a one-page note. The numbers are not judged good, and nothing here modernizes any system. The scoreboard measures delivery, it does not fix it.

Primary role: DevOps and DevSecOps. Tier: Foundation. It is one live build, budgeted to 45 minutes, with the seeded history and ground truth pre-staged so the session buys the collector and the metric argument, not the corpus.

## Architecture

```mermaid
---
title: Delivery Scoreboard data flow
---
%%{init: {"theme":"base","themeVariables": {"primaryColor":"#1B4332","primaryTextColor":"#F4D03F","primaryBorderColor":"#F4D03F","secondaryColor":"#264653","tertiaryColor":"#2F5233","lineColor":"#F4D03F","fontFamily":"ui-monospace, SFMono-Regular, Menlo, Consolas, monospace","fontSize":"13px"}}}%%
flowchart TD
    classDef datastore fill:#264653,stroke:#F4D03F,stroke-width:2px,color:#FFFFFF
    classDef service fill:#1B4332,stroke:#F4D03F,stroke-width:2px,color:#F4D03F
    classDef event fill:#7B42BC,stroke:#F4D03F,stroke-width:2px,color:#FFFFFF
    classDef io fill:#0d1117,stroke:#F4D03F,stroke-width:1.5px,color:#F4D03F,font-style:italic

    subgraph StarterRepo["Starter repo fixtures"]
        SeededHistory[("Captured 30-day REST API fixture")]
        GroundTruth[("20 hand-labelled deploys")]
        RequirementsPins[("requirements.txt: DuckDB, matplotlib, Python 3.12")]
    end

    subgraph Collection["Collection into DuckDB"]
        Collector(["Python collector over the gh CLI"])
        DuckDB[("DuckDB store")]
    end

    subgraph LabelGate["AI labelling gate"]
        AILabeler(["AI incident vs planned labeler"])
        ScoreLabels{{"score labels vs ground truth"}}
        Gate{{"gate at 18 of 20"}}
    end

    subgraph Metrics["Five metrics in SQL"]
        SQLCompute(["SQL over DuckDB"])
        DeployFreq[("deployment frequency")]
        LeadTime[("lead time for changes")]
        ChangeFailRate[("change failure rate")]
        TimeToRestore[("time to restore")]
        ReworkRate[("rework rate")]
    end

    subgraph Outputs["Validated outputs"]
        ExactMatch{{"exact-match check, no tolerance"}}
        Scoreboard[/"matplotlib scoreboard"/]
        PMReadout[/"PM readout"/]
        NotProveNote[/"what each metric does not prove"/]
    end

    SeededHistory -- "read by" --> Collector
    RequirementsPins -- "pins runtime for" --> Collector
    Collector -- "writes deploys, PRs, labels" --> DuckDB
    DuckDB -- "deploys to classify" --> AILabeler
    AILabeler -- "proposed labels" --> ScoreLabels
    GroundTruth -- "scored against" --> ScoreLabels
    ScoreLabels -- "result to" --> Gate
    Gate -- "admit at 18 of 20" --> DuckDB
    Gate -- "below 18 rejected, reported" --> NotProveNote
    DuckDB -- "SQL query" --> SQLCompute
    SQLCompute -- "computes" --> DeployFreq
    SQLCompute -- "computes" --> LeadTime
    SQLCompute -- "computes" --> ChangeFailRate
    SQLCompute -- "computes" --> TimeToRestore
    SQLCompute -- "computes from labels" --> ReworkRate
    DeployFreq -- "integer checked" --> ExactMatch
    LeadTime -- "median to the second" --> ExactMatch
    ReworkRate -- "15 percent on 3 of 20" --> ExactMatch
    GroundTruth -- "hand count" --> ExactMatch
    ExactMatch -- "passes, feeds" --> Scoreboard
    ChangeFailRate -- "renders into" --> Scoreboard
    TimeToRestore -- "renders into" --> Scoreboard
    Scoreboard -- "presented as" --> PMReadout
    Scoreboard -- "shipped with" --> NotProveNote

    class SeededHistory,GroundTruth,RequirementsPins,DuckDB,DeployFreq,LeadTime,ChangeFailRate,TimeToRestore,ReworkRate datastore
    class Collector,AILabeler,SQLCompute service
    class ScoreLabels,Gate,ExactMatch event
    class Scoreboard,PMReadout,NotProveNote io
```

Every number comes off timestamps the team can hand-count: the collector loads the captured fixture into DuckDB, SQL computes the five metrics, and the AI only classifies incident-versus-planned once its labels clear the 18 of 20 gate.

## Implementation

1. Open the kickoff Issues on the GitHub Project board, then draft the requirements brief, the four MADR decision records, and the Mermaid data-flow diagram with the AI and correct them by hand (8 minutes).
2. Run the Python collector to read the captured 30-day GitHub REST API fixture for deploys, pull requests, and incident labels, and write them into DuckDB (9 minutes).
3. Run SQL over DuckDB to compute deployment frequency, lead time for changes, change failure rate, time to restore, and rework rate (8 minutes).
4. Have the AI label each deploy incident-versus-planned, score the labels against the 20 hand-labelled deploys, and admit them into the metric only at 18 of 20 (7 minutes).
5. Render the scoreboard with matplotlib and validate the numbers against the hand-counted ground truth exactly (7 minutes).
6. Present the readout to the PM, write the teach-back one-pager and the after-action review, close every Issue by pull request, and tag the release (6 minutes).

## Validation

- ✅ Deployment frequency matches the hand-counted integer exactly against the seeded 30-day history.
- ✅ Median lead time for changes matches the hand-counted value to the second, with no tolerance band.
- ✅ Three incident-triggered deploys out of twenty return a rework rate of exactly 15 percent.
- ✅ The AI incident-versus-planned labels score at least 18 of 20 against the hand-labelled deploys before they enter the metric.
- ✅ Negative test: a run below 18 of 20 is rejected and reported as its count, and must not move rework rate.
- ✅ Every figure traces to a row in DuckDB, which is what makes the exact-match check possible.
- ✅ A one-page note ships stating what each metric does and does not prove, including that nothing here modernizes any system.
