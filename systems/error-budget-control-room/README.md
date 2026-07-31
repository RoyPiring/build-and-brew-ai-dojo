# Error Budget Control Room: Prove a Page Is Worth Waking Someone

> Inside the [Build & Brew: The AI Dojo](../../README.md) cohort · *A 45-minute live build toward the high-performing solutions engineer.*

## Overview

The incident review just ended. Two people carry the pager for this service, and both were awake last night for an alert that resolved itself before either finished logging in. The engineering manager wants the pages made defensible, not quieter: a number that says whether a given page was worth waking someone, and the derivation behind it. If it cannot be derived in front of her, it does not belong in the runbook.

This build turns a service level objective into an error budget and the burn-rate rules that decide whether a page is worth a human. It produces an availability SLI written as good events over valid events, an SLO of 99.9 percent over thirty days with its budget of about 43 minutes a month, two multiwindow multi-burn-rate rule sets generated from one OpenSLO source (a fast page and a slower rule), a Grafana burn-down panel, and a hand-worked derivation a person can defend out loud. It proves the arithmetic by replaying an incident: a page that resolved itself before anyone logged in.

The primary role is Site Reliability Engineering at Cohort tier, delivered as one live 45-minute build. The lab runs on compressed observation windows, because a one-hour burn window does not fit inside the session, and it says so: the production rule set carries the SRE Workbook's real thresholds and is hand-verified and never fired, while the lab set holds the identical burn-rate multipliers and window ratio at a twelvefold compression.

## Architecture

```mermaid
---
title: Error Budget Control Room topology
---
%%{init: {"theme":"base","themeVariables": {"primaryColor":"#1B4332","primaryTextColor":"#F4D03F","primaryBorderColor":"#F4D03F","secondaryColor":"#264653","tertiaryColor":"#2F5233","lineColor":"#F4D03F","fontFamily":"ui-monospace, SFMono-Regular, Menlo, Consolas, monospace","fontSize":"13px"}}}%%
flowchart TD
  subgraph assignment ["Assignment"]
    emAsk["Engineering manager's ask"]
    pagerRoster["Two-person pager roster"]
    objective["99.9% SLO, about 43 min budget"]
  end
  subgraph sourceOfTruth ["Source of truth"]
    workbookTable["SRE Workbook burn-rate table"]
    openSlo["OpenSLO source of truth"]
    sloth["Sloth generate"]
    prodRules["Production rules: 14.4x over 1h/5m"]
    labRules["Lab rules: compressed 12x"]
  end
  subgraph liveStack ["Live stack, one compose file"]
    cleanCloneGate["Clean clone: docker compose up green"]
    k6["k6 load and error injection"]
    sampleService["Sample service with error switch"]
    prometheus["Prometheus: metrics and rules"]
    grafana["Grafana burn-down panel"]
  end
  subgraph alerting ["Burn-rate alerting"]
    fastAlert["Fast-burn rule fires: page"]
    slowAlert["Slow-burn rule: silent in window"]
  end
  subgraph proof ["Human proof"]
    derivationCheck["Hand-worked derivation vs generated"]
    pageDecision["Is this page worth waking someone?"]
    teachBack["Teach-back README and readout"]
  end

  emAsk -- "sets the assignment" --> objective
  objective -- "stated in OpenSLO" --> openSlo
  objective -- "budget the panel drains" --> grafana
  openSlo -- "sloth generate" --> sloth
  sloth -- "emits production set" --> prodRules
  sloth -- "emits lab set" --> labRules
  workbookTable -- "hand-verified against" --> prodRules
  workbookTable -- "supplies real thresholds" --> derivationCheck
  prodRules -- "loaded, never fired" --> prometheus
  labRules -- "loaded and fired" --> prometheus
  derivationCheck -- "compares to generated" --> labRules
  cleanCloneGate -- "starts the live stack" --> k6
  cleanCloneGate -- "one command, stack green" --> sampleService
  k6 -- "drives 10% error rate" --> sampleService
  sampleService -- "/metrics scrape" --> prometheus
  prometheus -- "burn-rate query" --> grafana
  prometheus -- "fast-burn rule" --> fastAlert
  prometheus -- "slow-burn rule" --> slowAlert
  grafana -- "drains at derived slope" --> pageDecision
  fastAlert -- "arrives first" --> pageDecision
  slowAlert -- "no page in window" --> pageDecision
  pagerRoster -- "raises the cost of a page" --> pageDecision
  pageDecision -- "written for a non-SRE" --> teachBack
  derivationCheck -- "defended out loud" --> teachBack

  classDef datastore fill:#264653,stroke:#F4D03F,stroke-width:2px,color:#FFFFFF
  classDef service fill:#1B4332,stroke:#F4D03F,stroke-width:2px,color:#F4D03F
  classDef event fill:#7B42BC,stroke:#F4D03F,stroke-width:2px,color:#FFFFFF
  classDef io fill:#0d1117,stroke:#F4D03F,stroke-width:1.5px,color:#F4D03F,font-style:italic
  class emAsk,pagerRoster,pageDecision,teachBack io
  class objective,cleanCloneGate,fastAlert,slowAlert,derivationCheck event
  class workbookTable,openSlo,prodRules,labRules,prometheus datastore
  class sloth,k6,sampleService,grafana service
```

One OpenSLO document is the single source: Sloth generates both rule sets from it, k6 drives errors into the sample service, Prometheus scrapes it and evaluates the rules, and the fast-burn alert, the slow-burn alert, and the Grafana burn-down panel together answer whether the page was worth waking someone, with the hand-worked derivation verifying the thresholds against the SRE Workbook table.

## Implementation

1. Confirm the day-zero gate, run `docker compose up` to bring Prometheus, Grafana, Sloth, k6, and the sample service green, open the GitHub Projects board with one epic and five issues, and let the AI draft three of the four decision records from the stated constraints, corrected by hand.
2. Define the availability indicator as good events over valid events, argue the denominator out loud (valid excludes health checks and client cancellations), and reject one AI candidate SLI in writing.
3. Write the OpenSLO document at the openslo/v1alpha version and run Sloth to generate both the production and lab rule sets from that single source.
4. Derive the burn-rate thresholds by hand against the SRE Workbook table in `reference/workbook-burnrate-table.md` and compare them to the generated output, treating any mismatch as a finding.
5. Drive a sustained ten percent error rate with k6 so the scaled fast-burn rule pages, the scaled slow rule stays silent, and the Grafana burn-down panel drains.
6. Produce the three-slide error-budget policy readout and the teach-back README explaining burn rate to a non-SRE, close each issue by pull request carrying `Closes #n`, tag the release, and finish the docs.

## Validation

✅ From a clean clone, one command (`docker compose up`) brings the whole stack green (AC-1).
✅ At a sustained ten percent error rate, the scaled fast-burn rule fires exactly once inside its five-minute long window (AC-2).
✅ The scaled slow rule has not fired inside that window, because its thirty-minute window has not yet elapsed (AC-3).
✅ The Grafana burn-down panel drains within ten percent of the slope the hand-worked derivation predicted (AC-4).
✅ The hand-worked derivation shows the production 14.4x multiplier over a one-hour long window and the lab scaling factor side by side and states in one sentence why they are the same rule.
✅ The production rule set is hand-verified against the SRE Workbook table and never fired, while the lab set holds the identical multipliers and long-to-short window ratio at a twelvefold compression.
✅ The AI is scored, not just used: the readout reports how many of three candidate SLIs were rejected as unmeasurable and how many of six generated values the hand-derivation contradicted, and a zero on both is reported as the finding.
