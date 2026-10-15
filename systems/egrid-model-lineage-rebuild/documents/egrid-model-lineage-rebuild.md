<img src="https://cdn.prod.website-files.com/677c400686e724409a5a7409/6790ad949cf622dc8dcd9fe4_nextwork-logo-leather.svg" alt="NextWork" width="300" />

# Rebuild an EPA eGRID Model

**Project Link:** [View Project](https://nextwork.ai/projects/74b8ca97-0a10-4517-b739-efc131b54f57)

**Author:** Roy Piring: Cloud Platform Engineer | Build Master  
**Email:** rpiringhawaii@gmail.com

---

![Image](https://nextwork.ai/pineapple_king_overclocked_the_ai/uploads/74b8ca97-0a10-4517-b739-efc131b54f57_aqdvd61w)

## Setting Up a Reproducible Lineage Lab

### Defining the project goal

I built a local lineage lab to prove which source code and EPA eGRID2023 workbook produced each classifier. Git identified the code history, DVC tracked two workbook revisions by MD5 fingerprint, and MLflow retained the training runs and registered model versions. Each accepted model therefore needed more than a filename or performance score.

The evidence chain connected a model to one Git commit, one workbook hash, and one registry version. I also tested whether a clean clone could restore the correct data and reproduce the registered champion. This mattered because a strong score alone could not reveal that the wrong workbook had been used. Reproducibility required another person to restore the same code and bytes, rerun training, and receive the expected checks.

### Preparing the local environment

I created a Python 3.13 virtual environment and pinned MLflow 3.17.1, DVC 3.67.1, and four additional packages. I initialized Git and DVC, then configured an external DVC remote for the workbook bytes. This separated code history, data storage, and model tracking before any training run began.

I wrote the installed versions and setup results to check-out.txt, giving later lineage claims a known runtime. The environment record showed which tools built the models and provided a baseline for the clean-clone reconstruction. Pinning the dependencies also helped isolate package behavior from code or data changes when the first training run encountered a model-logging error.

![Image](https://nextwork.ai/pineapple_king_overclocked_the_ai/uploads/74b8ca97-0a10-4517-b739-efc131b54f57_cenwvkk0)

![Image](https://nextwork.ai/pineapple_king_overclocked_the_ai/uploads/74b8ca97-0a10-4517-b739-efc131b54f57_udh9ymup)

## Designing the Evidence Chain

### Mapping the lineage evidence

I documented the evidence contract before writing model code. The design set contained a requirements brief with AC-1 through AC-6, eight decision records, a Mermaid topology, a 45-minute build plan, and a source map containing 31 links. Each document assigned a specific role to Git, DVC, MLflow, the schema gate, and the clean-clone checks.

This sequence prevented the finished score from deciding what later counted as proof. The acceptance criteria defined the required commits, hashes, events, failures, and recovery evidence before training began. The source map also connected technical decisions to their supporting references. Later artifacts could therefore be checked against a written evidence plan rather than accepted because the model produced a favorable ROC AUC.

![Image](https://nextwork.ai/pineapple_king_overclocked_the_ai/uploads/74b8ca97-0a10-4517-b739-efc131b54f57_cm00w9zn)

### Tracing AC-6 to the final readout

AC-6 named the exact log evidence required before the final readout could mark the work complete. It required one train.py commit, two different registration hashes, two source SCHEMA OK events, the clean-clone passes, the exit 1 stale-data failure, and the later recovery. These were observable events rather than general statements about reproducibility.

The readout could tick an item only when check-out.txt contained the matching line. This made the summary dependent on retained evidence and blocked it from reporting an unmeasured success. If a hash, clean-clone pass, deliberate failure, or recovery line was absent, the related acceptance item remained open regardless of whether the final model score looked correct.

## Training and Tracking Revision 1

### Building the first training pipeline

I tracked the Revision 1 EPA eGRID2023 workbook at data/egrid.xlsx with DVC, committed its pointer, and added the training stage. Running dvc repro executed the schema check and training pipeline. The run printed SCHEMA OK for 3,274 plants and produced a ROC AUC of 0.9137.

This established the first model baseline with both code and data lineage. The Git commit identified the training implementation, while the DVC pointer identified the workbook revision used by that implementation. Later runs could now be compared against a known code state, source population, schema result, and performance value rather than an untracked local file and a standalone score.

### Connecting DVC data pointers to Git history

The DVC pointer recorded the workbook’s MD5 fingerprint and size in Git without committing the 21 MB Excel file itself. For Revision 1, data/egrid.xlsx.dvc stored the MD5 value 932a841d. The Track eGRID2023 revision 1 commit preserved that pointer beside the relevant pipeline history.

This meant the commit referenced the exact source bytes even though the workbook remained outside the Git repository. A clean clone could retrieve the file from the configured DVC remote and verify it against the pointer. Git supplied the lightweight history, while DVC connected that history to the larger data artifact required to reproduce the training run.

![Image](https://nextwork.ai/pineapple_king_overclocked_the_ai/uploads/74b8ca97-0a10-4517-b739-efc131b54f57_qlz3pth4)

## Registering Revision 2 and Testing Lineage Checks

### Registering a new data revision

I registered model version 1 from the Revision 1 run, replaced the local workbook with Revision 2, retrained the classifier, and registered model version 2. I then moved the champion alias to Revision 2, pushed the workbook bytes to the DVC remote, committed check.py, and created a fresh Git clone for reconstruction testing.

The registry now contained two durable versions tied to different data hashes. The champion alias resolved to the selected Revision 2 model rather than a run identifier that a reviewer had to locate manually. This allowed check.py to compare the clone’s code, data fingerprint, and ROC AUC with one curated registry target while preserving version 1 as earlier lineage evidence.

![Image](https://nextwork.ai/pineapple_king_overclocked_the_ai/uploads/74b8ca97-0a10-4517-b739-efc131b54f57_c5jit41e)

![Image](https://nextwork.ai/pineapple_king_overclocked_the_ai/uploads/74b8ca97-0a10-4517-b739-efc131b54f57_i2opfwmj)

### Proving the clean-clone safeguard

The fresh Git clone contained the source code and DVC metadata but not the workbook bytes used by the champion. When I ran check.py, the commit check and ROC AUC check passed, but the script printed FAIL data_md5 matches data/egrid.xlsx because the Excel file was missing from the clone.

This was the expected safeguard. It showed that Git alone did not carry enough material to rebuild or verify the model and that the DVC remote served an active lineage role rather than acting only as an extra backup. The failed MD5 check prevented the clone from being accepted merely because its code and recorded metric matched the registered champion.

## Rebuilding the Champion from Verified Artifacts

### Reconstructing the champion in a clean clone

Inside the clean clone, I ran dvc pull to restore the Revision 2 workbook from the external remote. I then forced dvc repro to execute training again and ran check.py. The checker printed three PASS lines followed by ALL CHECKS PASS, completing the planned reconstruction.

The result showed that the registered champion was not dependent on files left inside my original working directory. Git restored the code and DVC metadata, while the remote restored the exact workbook bytes. Rebuilding from those artifacts reproduced the expected lineage checks in a separate clone, providing stronger evidence than rerunning the pipeline inside the original workspace.

### Recovering data from the DVC remote

The DVC pointer named the exact Revision 2 workbook required by the champion. Running dvc pull reported 1 file fetched and 1 file added, restoring data/egrid.xlsx inside the clean clone. The recovered file hashed to 2449e137, matching the MD5 value registered with model version 2.

After the bytes matched, the forced training run produced the same ROC AUC of 0.914372 recorded for the champion. This connected the DVC transfer directly to the registry evidence. The reconstruction did not rely on a similarly named workbook or a copied local file. It used the source bytes identified by the pointer and confirmed by the checker.

## Investigating an Unregistered Diagnostic Run

### Identifying unregistered run evidence

I created a diagnostic stale-data run by passing the Revision 1 workbook through train.py and check.py without running register.py. Its METRICS line contained the Revision 1 hash 932a841d and a new model URI. The MLflow registry still contained only versions 1 and 2, and the champion alias continued resolving to version 2.

Because the diagnostic run was not registered, the champion’s expected MD5 remained 2449e137. check.py compared that fingerprint with the stale Revision 1 bytes and failed the match. If the run had been registered and promoted, the registry state would have changed. The unchanged versions and failing hash therefore provided evidence that this remained a tracking run rather than a registered model release.

![Image](https://nextwork.ai/pineapple_king_overclocked_the_ai/uploads/74b8ca97-0a10-4517-b739-efc131b54f57_oe0j2ssd)

### Understanding stale-data lineage failures

With Revision 1 copied into the clone, training still printed SCHEMA OK, and the ROC AUC check passed at 0.9137. Those results showed that the older workbook remained structurally valid and could still produce a credible model score. Neither check, by itself, proved that the workspace contained the data selected for the champion.

The MD5 comparison detected the stale bytes and returned exit code 1. This demonstrated why performance and schema validation could not replace source lineage. A score near the champion’s result might hide the wrong workbook, while the hash directly tested whether the local artifact matched the registered version. The deliberate failure protected the champion from being rebuilt with valid but unapproved data.

## Reflecting on Lineage and Reproducibility Skills

### Tools and concepts mastered

I used Git to identify source code history, DVC to track and restore workbook bytes, and MLflow to retain training runs and registered model versions. I also created acceptance criteria, an executable schema gate, a champion alias, clean-clone reconstruction checks, and a deliberate stale-data test.

The central lesson was that a model identity required both code and data evidence. A filename or ROC AUC could be repeated by the wrong artifact, but the Git commit and MD5 fingerprint tied the model to specific inputs. MLflow then connected those inputs and metrics to a durable registered version. Together, the three tools produced a lineage chain that could be tested rather than merely described.

### Project experience and challenges

The full build took approximately one and a half hours, exceeding the 45-minute plan. Environment setup and package installation took about 20 minutes, the five design documents about 15 minutes, and the training plus clean-clone checks about 30 minutes. The Secret Mission and final readout required another 20 minutes.

Most of the added time came from a model-logging error during the first training run. I resolved it by pinning an older version of skops, then continued with the same evidence plan. Locating the Revision 1 workbook on the EPA site also slowed the work. Recording these delays preserved the difference between the planned duration and the time required to produce the final proof.

### Next learning goals

I learned the most from watching the checker reject the stale workbook even though schema validation and ROC AUC still passed. That failure turned lineage from a documentation idea into an executable control. It also showed why the data fingerprint needed to remain part of the champion check.

My next goal is to repeat the same evidence chain with a shared DVC remote and a hosted MLflow server. That setup would let a team restore the source bytes, inspect registered versions, and rebuild the champion from separate environments. I want to preserve the same commit, hash, schema, metric, clean-clone, failure, and recovery evidence while moving from one local workstation to shared infrastructure.

---

*Built with [NextWork](https://nextwork.ai) - [View this project](https://nextwork.ai/projects/74b8ca97-0a10-4517-b739-efc131b54f57)*
