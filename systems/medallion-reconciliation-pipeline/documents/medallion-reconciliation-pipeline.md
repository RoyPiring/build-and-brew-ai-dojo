<img src="https://cdn.prod.website-files.com/677c400686e724409a5a7409/6790ad949cf622dc8dcd9fe4_nextwork-logo-leather.svg" alt="NextWork" width="300" />

# Reconcile a Taxi Analytics Pipeline

**Project Link:** [View Project](https://nextwork.ai/projects/6f7aa348-9425-42f8-98d2-48db9c60a695)

**Author:** Roy Piring: Cloud Platform Engineer | Build Master  
**Email:** rpiringhawaii@gmail.com

---

![Image](https://nextwork.ai/pineapple_king_overclocked_the_ai/uploads/6f7aa348-9425-42f8-98d2-48db9c60a695_abynj6nb)

## Defining a Defensible Analytics Contract

### Pipeline objective and reconciliation limits

I built a local taxi analytics pipeline with dbt and DuckDB to test whether a three-layer system could defend a declared data contract. Bronze preserved the source-facing records, silver applied the shared trip-validity rule, and gold produced the accepted trip total. I then reconciled the number of valid bronze rows with the gold total. A zero difference showed that records passing the declared rule were represented consistently across the pipeline.

The evidence supported a narrow claim about implementation consistency. It proved that the bronze, silver, and gold layers followed the validity rule written into the build and that the reconciliation test could compare their results. It did not prove that the rule itself represented an objectively correct business definition of a valid taxi trip. That definition still required review by the people responsible for the data and its intended use.

## Establishing a Reproducible Local Workspace

### Local project setup

I created a local repository and an isolated development environment for the taxi analytics pipeline. The workspace contained the dbt configuration, model directories, reusable macros, automated tests, raw-data location, DuckDB database, task record, and supporting evidence. This structure gave each part of the build a defined location and kept the transformation logic separate from generated files, downloaded data, and local dependencies.

The setup established a repeatable foundation before I wrote the layer logic. It gave the later SQL models and reconciliation results a known Python environment, package set, database file, directory structure, and Git history. By creating those boundaries first, I could separate errors caused by tool installation or local configuration from defects inside the transformations. The repository also preserved how the system moved from setup through validation.

### Verifying the required tools

I ran python --version and git --version to confirm that the required tools were installed and available from the terminal. I also checked the configured Git user.name and user.email so future commits would carry the expected local identity. These checks documented the toolchain used for the build and reduced uncertainty if a later command failed because of the workstation rather than the pipeline logic.

I then created the lakehouse-day repository, initialized the Python virtual environment, and installed the required dbt and DuckDB packages. I created the needed folders, added .gitignore, and wrote TASKS.md to track the work. After setup, the repository contained the structure required for source files, models, macros, tests, logs, and evidence. This gave the pipeline an isolated and inspectable starting point.

![Image](https://nextwork.ai/pineapple_king_overclocked_the_ai/uploads/6f7aa348-9425-42f8-98d2-48db9c60a695_y4jwnd21)

### Keeping the pipeline fully local

DuckDB served as the embedded analytics engine for the pipeline. It processed the query logic on the local machine and stored the resulting tables in lakehouse.duckdb. No remote database, cloud storage platform, or hosted processing service was required. The Python virtual environment in .venv kept dbt, the DuckDB adapter, and related packages isolated from other Python installations on the workstation.

The .gitignore file kept downloaded Parquet files, .venv, dbt target output, logs, and the DuckDB database outside version control. Those working files remained on the local disk, while Git tracked the SQL models, macros, tests, configuration, tasks, and written evidence. This separation kept large or generated files out of repository history without losing the instructions and logic needed to recreate the build in another compatible local environment.

## Documenting the Solution Before Building

### Capturing the design contract

I documented the requirements, layer responsibilities, validation steps, and technical limits before writing the SQL transformations. The design defined bronze as the source-facing boundary, silver as the layer that applied the reusable validity rule, and gold as the accepted trip total. It also defined reconciliation as a comparison between valid bronze rows and the gold result, giving the build a measurable acceptance condition before execution.

Writing the contract first gave the implementation a fixed target. The SQL models and tests could be judged against decisions recorded before their results were visible. This order reduced the risk of changing the explanation after seeing the output or treating any successful run as proof of correctness. It also established the limit of the claim: reconciliation could prove that the layers agreed with the declared rule, but it could not independently prove that the business definition itself was correct.

![Image](https://nextwork.ai/pineapple_king_overclocked_the_ai/uploads/6f7aa348-9425-42f8-98d2-48db9c60a695_capdgrzs)

## Building the Bronze-to-Gold Pipeline

### Creating the layered dbt transformation path

I created a reusable valid_trip() macro and used it to establish one declared validity rule for the pipeline. The bronze model exposed the source-facing taxi records in an inspectable form. Silver applied the macro as its filtering boundary, and gold read the accepted records from silver to produce the trip total used in the final comparison. This path made each layer’s responsibility visible instead of hiding all transformation logic in one query.

The shared macro reduced the risk of describing validity differently in separate parts of the build. Bronze retained the population needed for inspection, silver decided which rows met the declared conditions, and gold summarized the records that passed. The reconciliation test independently counted valid bronze rows and compared that count with gold. This structure connected the source boundary, filtering decision, final total, and acceptance test without changing the raw input files.

![Image](https://nextwork.ai/pineapple_king_overclocked_the_ai/uploads/6f7aa348-9425-42f8-98d2-48db9c60a695_i36srci9)

## Proving the Layer Contract Catches Defects

### Validating reconciliation and repeatability

I first ran the complete pipeline and confirmed that the valid bronze count matched the gold trip total. That successful baseline showed a zero-difference reconciliation under the declared layer contract. I then created a controlled defect by removing only the valid_trip() filter from the silver model. Gold continued reading from silver, and the reconciliation test continued counting valid bronze rows, so the experiment changed one enforcement boundary while leaving the comparison logic intact.

The deliberate defect tested whether the reconciliation could reject a known violation instead of merely producing a passing result. Comparing the original pass with the expected failure provided evidence that the test responded to a meaningful layer change. It also made the cause traceable: silver stopped enforcing its assigned rule, gold received a broader row set, and the unchanged bronze-side count exposed the difference. This sequence showed repeatable behavior rather than relying on one successful run.

![Image](https://nextwork.ai/pineapple_king_overclocked_the_ai/uploads/6f7aa348-9425-42f8-98d2-48db9c60a695_obmtqbzf)

### Evidence from the deliberate filter failure

Removing only the silver valid_trip() filter caused silver to pass every bronze row forward instead of retaining only rows that met the declared validity conditions. Because gold still read from silver, its total expanded to include those additional rows. The reconcile_gold_to_bronze test did not change. It continued counting only valid bronze rows, so the expected total and the gold total no longer matched.

The reconciliation returned one result, and the build exited with code 1. This was an expected failure created for validation, not evidence that the completed pipeline remained broken. It proved that the test could detect the exact condition it was designed to catch: silver no longer enforcing its layer rule. The isolated change also ruled out a rewritten test or altered source population as the cause. The failed run connected the mismatch directly to the removed filter.

## Restoring and Defending the Reconciled Solution

### Restoring the validity rule and closing out

I restored the valid_trip() filter in the silver model after recording the expected failure. I then reran the models and tests with the same local source files, dbt configuration, DuckDB database path, and reconciliation logic. Once silver resumed enforcing the declared rule, gold again represented the accepted record set, and the comparison between valid bronze rows and gold returned to a zero-difference passing state.

The final evidence contained three connected results: the original baseline pass, the deliberate failure caused by removing one filter, and the restored pass after replacing it. This sequence supported a stronger defense than a passing run by itself. It showed what the correct system produced, how the test reacted when the layer contract was violated, and whether returning the implementation to the accepted design also returned the pipeline to its expected result.

### Explaining how the failure proved the contract was active

The must-fail run removed only the silver valid_trip() filter. Gold still selected its records from silver, while reconcile_gold_to_bronze continued calculating the expected total from valid bronze rows. Because the filter was the boundary that kept invalid rows from moving forward, deleting it created a count difference without requiring any change to gold, the source data, or the test itself. The build returned one failed result and exit code 1.

That outcome demonstrated that the contract was active in execution rather than present only in documentation. If the reconciliation had still passed, it would not have defended the silver boundary. Instead, the unchanged test detected the violation as soon as silver stopped applying its rule. Restoring the filter removed the mismatch and returned the build to a pass, tying both the failure and recovery to the same controlled condition.

## Extending the Pipeline to a Second Month

![Image](https://nextwork.ai/pineapple_king_overclocked_the_ai/uploads/6f7aa348-9425-42f8-98d2-48db9c60a695_6kv7dffw)

### Comparing changed volumes against an unchanged rule

I added the June Parquet file beside the existing July file, increasing the number of landed files from 1 to 2. I then reran the pipeline without changing the bronze, silver, or gold logic. Their row counts remained 3,530,109 for bronze, 3,388,734 for silver, and 3,388,734 for gold. The additional physical file therefore changed the contents of the data directory without changing the population accepted by the July pipeline.

The active boundary still required pickup timestamps from 2026-07-01 up to but not including 2026-08-01, with trip_distance > 0 and total_amount >= 0. Stable layer counts showed that June did not silently enter the July result after landing beside it. This check separated file arrival from model scope: the source location could contain another month while the written rule continued selecting only the intended July records.

## Reflecting on Local Analytics Engineering

### Tools and concepts learned

I used dbt Core to define, order, and test the transformation models, DuckDB as the embedded local analytics engine, Git to retain the build history, and a Python virtual environment to isolate dependencies. These tools supported a fully local workflow in which the source files, database, compiled output, and logs remained on the workstation while the transformation logic and evidence stayed inspectable in the repository.

The build gave me hands-on experience with bronze, silver, and gold responsibilities, reusable validity logic, and automated reconciliation between layers. I also learned that a defensible analytics result needs more than a successful build. The intentional filter removal proved that the test could reject a known defect, while the restored run showed recovery. Together, those results explained what was tested, why the check mattered, and what the evidence did not claim.

### Project duration and challenges

I completed the build in approximately 60 minutes. The most demanding part was adding the June Parquet file to the same data directory while keeping the bronze model restricted to the July population. The physical file count needed to change from 1 to 2 without causing the July source boundary to widen or altering downstream totals. That required careful comparison of the layer counts before and after the new file arrived.

I confirmed that bronze remained at 3,530,109 rows and that silver and gold both remained at 3,388,734. Those unchanged counts showed that the written July date boundary still controlled ingestion despite the added file. The exercise highlighted why a pipeline should prove both what it includes and what it excludes. File presence alone did not determine scope; the declared model conditions controlled which records entered the analytics result.

### Next learning goals

I completed this work to build practical experience with a local medallion pipeline using dbt and DuckDB. The build covered environment isolation, source-facing bronze records, a reusable silver validity rule, gold aggregation, zero-difference reconciliation, a deliberate contract violation, restoration, and a second-month boundary check. It also required the evidence to distinguish pipeline consistency from the separate question of whether the business validity rule was correct.

My next goal is to carry these patterns into cloud-native analytics platforms such as Snowflake or BigQuery. I want to preserve the same layer ownership, narrow acceptance claims, controlled failure tests, and retained evidence while introducing remote storage and larger execution environments. That next step should test whether the contract remains traceable when compute, storage, permissions, and orchestration are no longer confined to one local machine.

---

*Built with [NextWork](https://nextwork.ai) - [View this project](https://nextwork.ai/projects/6f7aa348-9425-42f8-98d2-48db9c60a695)*
