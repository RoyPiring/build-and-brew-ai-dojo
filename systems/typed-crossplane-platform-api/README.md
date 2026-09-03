# Build a Typed Crossplane v2 API

> Inside the [Build & Brew: The AI Dojo](../../README.md) cohort · *A live build toward the high-performing solutions engineer.*

## Overview

Asking a developer to hand-assemble a Deployment and a Service for every application is asking them to re-derive the platform's opinions each time, and to get them wrong differently each time. This build replaces that with one namespaced Crossplane v2 API. An ApplicationEnvironment carries application identity, container image, port, and replicas, and a Composition turns an accepted request into the Kubernetes objects behind it through Function Patch and Transform and provider-kubernetes, on a local kind cluster running Crossplane 2.4.0.

The contract came before the implementation, fixing the boundary, the required behaviors, and the evidence that would settle the result, so no later outcome could define its own success. That discipline also governed the AI: the model's proposal was saved unchanged into an evidence file and never converted directly into an XRD, a Composition, or a function, with corrections written separately in the readout. The proposal, the human filter, and the shipped policy stay three visible things rather than one, and an unreviewed answer does not become cluster policy.

Two checks carry the build, testing opposite halves of that contract. Admission proves refusal: an unpinned image tag failed the image rule during a server-side dry run, so nothing was stored and no Deployment or Service work began, which is refusal at the request boundary rather than repair after the fact. Reconciliation proves persistence: deleting the queue-api Deployment returned NotFound while the parent request and the Service stayed in the cluster, and Crossplane restored it to 1/1 with the rollout complete and no second apply. A one-time generator could produce the first Deployment, but it cannot explain the return. The trap sits alongside them, because a legacy v1 XRD passed its own dry run with exit code 0. Admission alone never proves a pattern is current, and only a scope script reading the live XRD and printing Namespaced confirmed the v2 selection.

## Architecture

```mermaid
---
title: Build a Typed Crossplane v2 API
---
%%{init: {"theme":"base","themeVariables": {"primaryColor":"#1B4332","primaryTextColor":"#F4D03F","primaryBorderColor":"#F4D03F","secondaryColor":"#264653","tertiaryColor":"#2F5233","lineColor":"#F4D03F","fontFamily":"ui-monospace, SFMono-Regular, Menlo, Consolas, monospace","fontSize":"13px"}}}%%
flowchart TD
    classDef datastore fill:#264653,stroke:#F4D03F,stroke-width:2px,color:#FFFFFF
    classDef service fill:#1B4332,stroke:#F4D03F,stroke-width:2px,color:#F4D03F
    classDef event fill:#7B42BC,stroke:#F4D03F,stroke-width:2px,color:#FFFFFF
    classDef io fill:#0d1117,stroke:#F4D03F,stroke-width:1.5px,color:#F4D03F,font-style:italic

    Developer[/Developer declaring one application environment/]
    Platform[/Platform team owning the contract/]

    subgraph Contract["The contract, fixed before the implementation"]
        Pack[("Boundary, required behaviors, acceptance criteria, evidence")]
        BeforeFact{{"Written first, so no later result can define success afterwards"}}
        Draft[("rules-draft: the model's proposal saved unchanged")]
        Readout[("readout: the human corrections, kept separate")]
        NeverDirect{{"No AI response converted straight into a platform file"}}
    end

    subgraph Plane["Local control plane"]
        Kind("kind cluster on Docker")
        Crossplane("Crossplane 2.4.0, installed by Helm")
        PatchFn("Function Patch and Transform")
        ProviderK("provider-kubernetes")
        ProviderCfg[("ProviderConfigs, so the provider can manage in-cluster objects")]
    end

    subgraph Api["The typed request surface"]
        Xrd[("Namespaced ApplicationEnvironment XRD")]
        Fields[("Application identity, container image, port, replicas")]
        Cel{{"Field-level CEL rules evaluated by the Kubernetes API server"}}
    end

    subgraph Admission["Refusal happens before storage"]
        DryRun("Server-side dry run: same admission path, no write")
        BadImage[("The latest tag rejected on the image rule")]
        NotStored{{"Nothing stored, because the request never passed the boundary"}}
        NoWork{{"No Deployment or Service work began, so there is nothing to repair"}}
    end

    subgraph Reconcile["Acceptance starts the pipeline"]
        Stored[("Valid request persisted")]
        Pipeline("Composition pipeline")
        Deployment[("queue-api Deployment")]
        Service[("Service")]
    end

    subgraph Drift["The deletion test"]
        Delete("Deleted the queue-api Deployment")
        Gone[("kubectl returned NotFound")]
        ParentIntact{{"ApplicationEnvironment and Service stayed, so the request outlived its resource"}}
        Restored[("Deployment back at 1/1, rollout status complete")]
        NoApply{{"Returned with no second apply, which a one-time generator cannot explain"}}
    end

    subgraph Currency["Compatibility is not currency"]
        Legacy("Legacy v1 LegacyCluster dry run, kept as a control")
        ExitZero[("Passed admission with exit code 0")]
        ScopeScript("Scope script reading the live XRD")
        Namespaced[("Printed only Namespaced")]
        TwoQuestions{{"Admission asks whether Kubernetes accepts it, the rubric asks whether it is the current pattern"}}
    end

    Platform -- "writes" --> Pack
    Pack -- "is fixed by" --> BeforeFact
    Platform -- "prompts an assistant, saving the reply to" --> Draft
    Draft -- "reviewed into" --> Readout
    Readout -- "enforces" --> NeverDirect
    NeverDirect -- "keeps unreviewed rules out of" --> Cel
    Pack -- "specifies" --> Xrd
    Kind -- "hosts" --> Crossplane
    Crossplane -- "runs" --> PatchFn
    Crossplane -- "runs" --> ProviderK
    ProviderK -- "configured by" --> ProviderCfg
    Xrd -- "declares" --> Fields
    Fields -- "constrained by" --> Cel
    Xrd -- "registered into" --> Crossplane
    Developer -- "submits an ApplicationEnvironment to" --> Xrd
    Cel -- "evaluated during" --> DryRun
    DryRun -- "produced" --> BadImage
    BadImage -- "means" --> NotStored
    NotStored -- "therefore" --> NoWork
    Cel -- "passing requests become" --> Stored
    Stored -- "evaluated by" --> Pipeline
    PatchFn -- "transforms inside" --> Pipeline
    ProviderK -- "applies the output of" --> Pipeline
    Pipeline -- "creates" --> Deployment
    Pipeline -- "creates" --> Service
    Platform -- "then runs" --> Delete
    Delete -- "confirmed by" --> Gone
    Delete -- "left behind" --> ParentIntact
    Deployment -- "is the object removed by" --> Delete
    Crossplane -- "observed the drift and produced" --> Restored
    Restored -- "arrived without intervention, so" --> NoApply
    Platform -- "also runs" --> Legacy
    Legacy -- "returned" --> ExitZero
    ExitZero -- "shows a stale pattern is still accepted, hence" --> TwoQuestions
    Platform -- "answers the second question with" --> ScopeScript
    ScopeScript -- "read the live XRD and reported" --> Namespaced
    Namespaced -- "is what actually confirms the v2 selection in" --> TwoQuestions
    NoWork -- "satisfies the refusal criterion in" --> Pack
    NoApply -- "satisfies the reconciliation criterion in" --> Pack
    Deployment -- "is what was asked for, without writing it, for" --> Developer
    Service -- "is what was asked for, without writing it, for" --> Developer

    class Pack,Draft,Readout,Xrd,Fields,ProviderCfg,BadImage,Stored,Deployment,Service,Gone,Restored,ExitZero,Namespaced datastore
    class Kind,Crossplane,PatchFn,ProviderK,DryRun,Pipeline,Delete,Legacy,ScopeScript service
    class BeforeFact,NeverDirect,Cel,NotStored,NoWork,ParentIntact,NoApply,TwoQuestions event
    class Developer,Platform io
```

The diagram shows the topology and data flow of the system as built. The full architectural narrative, with screenshots and prose, lives in [`documents/typed-crossplane-platform-api.md`](./documents/typed-crossplane-platform-api.md).

## Implementation

This system is built across **6 phases**:

1. Reconciling a Healthy Application Environment
2. Enforcing a Typed API with Admission Controls
3. Defining and Preserving the Platform Contract
4. Launching a Healthy Local Control Plane
5. Testing Scope and Refusal Boundaries
6. Building Foundations for a Kubernetes Platform

For the full walkthrough with screenshots and step-by-step content, see [`documents/typed-crossplane-platform-api.md`](./documents/typed-crossplane-platform-api.md).

## Validation

Each build phase below is documented in [`documents/typed-crossplane-platform-api.md`](./documents/typed-crossplane-platform-api.md), with screenshots, configuration, and notes as captured during the build:

- ✅ Reconciling a Healthy Application Environment
- ✅ Enforcing a Typed API with Admission Controls
- ✅ Defining and Preserving the Platform Contract
- ✅ Launching a Healthy Local Control Plane
- ✅ Testing Scope and Refusal Boundaries
- ✅ Building Foundations for a Kubernetes Platform
