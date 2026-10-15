# The Score Was Right, the Data Was Wrong

> Inside the [Build & Brew: The AI Dojo](../../README.md) cohort · *A live build toward the high-performing solutions engineer.*

## Overview

A trained model is usually identified by a filename and a score, and both can be produced by the wrong data. This build makes that identity testable. A classifier is trained on the EPA eGRID2023 workbook, and every accepted model has to name one Git commit, one workbook MD5 fingerprint and one registry version, so a reviewer can tell which source bytes produced which model without taking the builder's word for it.

The evidence contract was written before any model code. A requirements brief carrying AC-1 through AC-6, eight decision records, a topology and a 45-minute plan were committed first, with a source map of 31 links tying each decision to its reference. AC-6 named the exact lines the final readout needed: one `train.py` commit, two different registration hashes, two source `SCHEMA OK` events, the clean-clone passes, the deliberate exit 1 failure and the later recovery. The readout could tick an item only when `check-out.txt` held the matching line, which is what stops a finished score from deciding afterwards what counts as proof.

Three tools were given three separate jobs. Git carries the source history, DVC tracks the workbook bytes by fingerprint, and MLflow retains the runs and the registered versions. Revision 1 printed `SCHEMA OK` for **3,274 plants** at a ROC AUC of **0.9137**, with the 21 MB workbook staying outside the repository and only its pointer, MD5 `932a841d`, committed to Git. Revision 2 was then tracked, retrained and registered as version 2 at MD5 `2449e137`, and the champion alias was moved to it so the checker compares against one chosen model rather than a run identifier a reviewer has to go and find.

The clean clone is where the claim was tested rather than asserted. A fresh clone held the code and the DVC metadata but none of the workbook bytes: the commit check passed, the ROC AUC check passed, and the run still failed on `data_md5`, which shows Git alone does not carry enough to rebuild or verify the model. Running `dvc pull` fetched **1 file**, the recovered workbook hashed to `2449e137`, and a forced retrain returned three `PASS` lines and `ALL CHECKS PASS` at a ROC AUC of **0.914372**. The champion was rebuilt from named artifacts rather than from files left behind in the original working directory.

The sharpest result is the one that was made to fail. A diagnostic run pushed the revision 1 workbook through training and checking without registering it: the old data still printed `SCHEMA OK` and still passed the ROC AUC check at **0.9137**, so on performance and schema alone it looked acceptable. The MD5 comparison rejected the stale bytes and returned **exit code 1**, and the registry still held only versions 1 and 2 with the champion resolving to version 2. A score close to the champion can hide the wrong workbook, which is why the fingerprint stays in the check. The clock is recorded honestly too: the build took about **one and a half hours** against a 45-minute plan, most of the overrun a model-logging error resolved by pinning an older `skops`, and the shared DVC remote and hosted MLflow server that would let a team do this from separate machines are named as owed rather than claimed.

## Architecture

```mermaid
---
title: The Score Was Right, the Data Was Wrong
---
%%{init: {"theme":"base","themeVariables": {"primaryColor":"#1B4332","primaryTextColor":"#F4D03F","primaryBorderColor":"#F4D03F","secondaryColor":"#264653","tertiaryColor":"#2F5233","lineColor":"#F4D03F","fontFamily":"ui-monospace, SFMono-Regular, Menlo, Consolas, monospace","fontSize":"13px"}}}%%
flowchart TD
    classDef datastore fill:#264653,stroke:#F4D03F,stroke-width:2px,color:#FFFFFF
    classDef service fill:#1B4332,stroke:#F4D03F,stroke-width:2px,color:#F4D03F
    classDef event fill:#7B42BC,stroke:#F4D03F,stroke-width:2px,color:#FFFFFF
    classDef io fill:#0d1117,stroke:#F4D03F,stroke-width:1.5px,color:#F4D03F,font-style:italic

    Accepter[/Whoever has to accept the champion without rerunning the training themselves/]
    Owed[/A shared DVC remote and a hosted MLflow server, so a team can do this from separate machines/]

    subgraph Contract["The evidence contract, written before any model code"]
        Brief[("A requirements brief carrying AC-1 through AC-6, eight decision records, a topology and a 45-minute plan")]
        Map[(A source map of 31 links tying each technical decision to its reference)]
        Before{{Writing the acceptance criteria first stops the finished score from deciding what later counts as proof}}
        AC6[("AC-6 names the exact lines required: one train.py commit, two registration hashes, two SCHEMA OK events, the clean-clone passes, the exit 1 failure and the recovery")]
        TickRule{{The readout may tick an item only when check-out.txt holds the matching line, so an unmeasured success cannot be reported}}
    end

    subgraph Lab["A pinned local lineage lab"]
        Venv(Python 3.13 virtual environment with MLflow 3.17.1, DVC 3.67.1 and four more packages pinned)
        Remote(An external DVC remote configured for the workbook bytes before any training run)
        CheckOut[(check-out.txt recording the installed versions, so later lineage claims have a known runtime)]
        Isolate{{Pinned dependencies separate package behavior from code or data changes}}
    end

    subgraph Roles["Three tools, three separate jobs"]
        Git(Git: the source code history)
        Dvc(DVC: the workbook bytes, tracked by MD5 fingerprint)
        Mlflow(MLflow: training runs and registered model versions)
        NotEnough{{A filename or a performance score can be produced by the wrong artifact, so neither identifies a model}}
    end

    subgraph Rev1["Revision 1, the first tracked baseline"]
        Book1[("EPA eGRID2023 revision 1 at data/egrid.xlsx, MD5 932a841d")]
        Pointer[("data/egrid.xlsx.dvc holds the fingerprint and size in Git; the 21 MB workbook never enters the repository")]
        Repro1(dvc repro running the schema gate and the training stage)
        Schema1[("SCHEMA OK for 3,274 plants, ROC AUC 0.9137")]
        Tied{{The commit references the exact source bytes although the workbook sits outside Git, and a clone can fetch and verify them}}
    end

    subgraph Rev2["Revision 2, and a champion worth pointing at"]
        V1[(Model version 1 registered from the revision 1 run)]
        Book2[("Revision 2 workbook swapped in, retrained, registered as version 2, MD5 2449e137")]
        Alias[(The champion alias moved to version 2)]
        Pushed(Workbook bytes pushed to the DVC remote and check.py committed)
        OneTarget{{The alias resolves to a chosen model rather than a run identifier a reviewer has to go and find}}
    end

    subgraph Clone["The clean clone, asked to prove it"]
        Fresh(A fresh Git clone holding the code and the DVC metadata but none of the workbook bytes)
        Partial[("Commit check PASS, ROC AUC check PASS, then FAIL data_md5 matches data/egrid.xlsx")]
        GitAlone{{Git alone does not carry enough to rebuild or verify the model, so the remote holds an active lineage role rather than a spare backup}}
    end

    subgraph Rebuild["Reconstruction from the named artifacts"]
        Pull(dvc pull reporting 1 file fetched and 1 file added)
        Match[("The recovered file hashes to 2449e137, matching the fingerprint registered with version 2")]
        Forced(A forced dvc repro training again inside the clone)
        Passed[("Three PASS lines, then ALL CHECKS PASS, at ROC AUC 0.914372")]
        NotLocal{{The champion does not depend on files left in the original working directory, which is the stronger claim}}
    end

    subgraph Stale["The deliberate stale-data run"]
        Diag(Revision 1 passed through train.py and check.py with register.py deliberately not run)
        Metrics[("The METRICS line carries the revision 1 hash 932a841d and a new model URI")]
        Unchanged[(The registry still holds only versions 1 and 2, and the champion alias still resolves to version 2)]
        Credible[("The old workbook still prints SCHEMA OK and still passes ROC AUC at 0.9137")]
        Caught[("The MD5 comparison rejects the stale bytes and returns exit code 1")]
        Lesson{{A score near the champion can hide the wrong workbook, so performance and schema validation cannot stand in for source lineage}}
        Protected{{The deliberate failure is what stops the champion being rebuilt from valid but unapproved data}}
    end

    subgraph Cost["The clock, recorded rather than rounded"]
        Plan[(Planned at 45 minutes, took about one and a half hours)]
        Split[("20 minutes environment, 15 minutes design documents, 30 minutes training and clone checks, 20 minutes readout")]
        Why[(Most of the overrun was a model-logging error, resolved by pinning an older skops, plus time finding the revision 1 workbook on the EPA site)]
        Honest{{Recording the delay preserves the difference between the planned duration and the time the proof actually cost}}
    end

    Accepter -- "cannot take a score on trust, so the build opens with" --> Brief
    Brief -- "supported by" --> Map
    Brief -- "exists because" --> Before
    Brief -- "sharpens into" --> AC6
    AC6 -- "binds the readout through" --> TickRule
    Before -- "applied first to" --> Venv
    Venv -- "paired with" --> Remote
    Venv -- "recorded in" --> CheckOut
    Venv -- "pinned so that" --> Isolate
    CheckOut -- "is where" --> TickRule
    Remote -- "gives its job to" --> Dvc
    Venv -- "gives its job to" --> Git
    Venv -- "gives its job to" --> Mlflow
    Git -- "exists because" --> NotEnough
    Dvc -- "tracks" --> Book1
    Book1 -- "represented in Git by" --> Pointer
    Pointer -- "committed, enabling" --> Tied
    Pointer -- "consumed by" --> Repro1
    Repro1 -- "prints" --> Schema1
    Mlflow -- "retains the run behind" --> Schema1
    Schema1 -- "registered as" --> V1
    Book1 -- "replaced by" --> Book2
    Book2 -- "retrained and registered, then" --> Alias
    Alias -- "gives check.py" --> OneTarget
    Book2 -- "bytes sent by" --> Pushed
    Pushed -- "makes possible" --> Fresh
    Fresh -- "checked, returning" --> Partial
    Partial -- "demonstrates" --> GitAlone
    GitAlone -- "answered by" --> Pull
    Pull -- "restores bytes that give" --> Match
    Match -- "clears the way for" --> Forced
    Forced -- "returns" --> Passed
    Passed -- "establishes" --> NotLocal
    OneTarget -- "is what Passed is measured against" --> Passed
    Alias -- "left untouched by" --> Diag
    Diag -- "writes" --> Metrics
    Diag -- "leaves" --> Unchanged
    Unchanged -- "is the evidence it stayed a tracking run" --> Metrics
    Diag -- "still produces" --> Credible
    Credible -- "would have passed, but" --> Caught
    Match -- "is the fingerprint" --> Caught
    Caught -- "teaches" --> Lesson
    Lesson -- "is why the failure is kept" --> Protected
    Protected -- "read by" --> Accepter
    NotLocal -- "read by" --> Accepter
    Passed -- "cost recorded as" --> Plan
    Plan -- "broken down as" --> Split
    Plan -- "explained by" --> Why
    Why -- "kept under" --> Honest
    NotLocal -- "bounded by one workstation, leaving" --> Owed
    Remote -- "local today, owed as" --> Owed

    class Brief,Map,AC6,CheckOut,Book1,Pointer,Schema1,V1,Book2,Alias,Partial,Match,Passed,Metrics,Unchanged,Credible,Caught,Plan,Split,Why datastore
    class Venv,Remote,Git,Dvc,Mlflow,Repro1,Pushed,Fresh,Pull,Forced,Diag service
    class Before,TickRule,Isolate,NotEnough,Tied,OneTarget,GitAlone,NotLocal,Lesson,Protected,Honest event
    class Accepter,Owed io
```

The diagram shows the topology and data flow of the system as built. The full architectural narrative, with screenshots and prose, lives in [`documents/egrid-model-lineage-rebuild.md`](./documents/egrid-model-lineage-rebuild.md).

## Implementation

This system is built across **6 phases**:

1. Setting Up a Reproducible Lineage Lab
2. Designing the Evidence Chain
3. Training and Tracking Revision 1
4. Registering Revision 2 and Testing Lineage Checks
5. Rebuilding the Champion from Verified Artifacts
6. Investigating an Unregistered Diagnostic Run

For the full walkthrough with screenshots and step-by-step content, see [`documents/egrid-model-lineage-rebuild.md`](./documents/egrid-model-lineage-rebuild.md).

## Validation

Each build phase below is documented in [`documents/egrid-model-lineage-rebuild.md`](./documents/egrid-model-lineage-rebuild.md), with screenshots, configuration, and notes as captured during the build:

- ✅ Setting Up a Reproducible Lineage Lab
- ✅ Designing the Evidence Chain
- ✅ Training and Tracking Revision 1
- ✅ Registering Revision 2 and Testing Lineage Checks
- ✅ Rebuilding the Champion from Verified Artifacts
- ✅ Investigating an Unregistered Diagnostic Run
