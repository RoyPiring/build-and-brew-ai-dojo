<img src="https://cdn.prod.website-files.com/677c400686e724409a5a7409/6790ad949cf622dc8dcd9fe4_nextwork-logo-leather.svg" alt="NextWork" width="300" />

# DORA Delivery Scoreboard with DuckDB

**Project Link:** [View Project](https://nextwork.ai/projects/f830ef72-575b-48d3-826a-8c99f0333e00)

**Author:** Roy Piring: Cloud Platform Engineer | Build Master  
**Email:** rpiringhawaii@gmail.com

---

![Image](https://nextwork.ai/refreshed_maroon_timid_jujube/uploads/f830ef72-575b-48d3-826a-8c99f0333e00_w4p28c1u)

## What This Project Is About

### Project overview and goals

I built a DORA delivery scoreboard with DuckDB and Python to analyze 30 days of deployment history. The system calculated deployment frequency, change lead time, change fail rate, failed deployment recovery time, and rework rate. I defined each metric in SQL so its result had a repeatable path back to the captured records.

I also added an AI gate that classified deployments as incident-triggered rework or planned work. The gate checked those predictions against sealed labels before releasing the rework metric.

The final output was a self-contained, offline HTML dashboard. It showed the delivery results while documenting the limits of each metric and what the available records could not prove. This kept the calculations, evidence, and constraints visible to anyone reading the scoreboard.

## Setting Up the Project and Writing Design Documents

### Step goals

I set up the build environment, installed DuckDB, and created the base code structure for the DORA scoreboard. This foundation connected the captured JSON data, SQL metric definitions, Python processing, AI classification gate, and final HTML output.

I documented the main design decision before calculating the metrics. The build needed a repeatable source that would return the same records during each run. A stable input prevented changes in repository state from changing the results while I tested the calculations.

The setup created a clear path from deployment records to SQL queries and then to scoreboard.html. It also made failures easier to trace. I could separate a data problem, calculation error, classification miss, or display issue instead of treating the scoreboard as one operation.

![Image](https://nextwork.ai/refreshed_maroon_timid_jujube/uploads/f830ef72-575b-48d3-826a-8c99f0333e00_1hl8nuz7)

### Decision record: chosen approach, alternative, and reversal trigger

ADR-001 selected a captured JSON corpus instead of calling a live repository API. I also considered a scheduled API cache, but the captured corpus gave this build the most repeatable source. Each run could use the same deployment records, incident reports, and sealed labels without depending on changing repository state.

This choice avoided authentication, pagination, and rate-limit handling. Those concerns matter for a live collector, but they were not required to prove the SQL definitions or AI gate.

The trade-off was freshness. A captured corpus does not update when new deployments or incidents occur, so it cannot represent current delivery performance without another capture. The decision should be reversed if the scoreboard becomes a recurring job that must reflect live state. At that point, a live API or scheduled cache would become necessary.

## Building the Captured Corpus

### Step goals

I created a captured corpus containing 20 deployment records, incident reports, and sealed labels. This fixed dataset supplied the information needed to calculate the five DORA metrics and test whether the AI gate could identify incident-triggered rework.

The deployment records supported frequency and time-based calculations. The incident reports supplied evidence for failure and recovery measurements. The sealed labels provided the reference source used to score the AI classifications before releasing the rework rate.

Keeping these records together made every run repeatable. DuckDB could process the same 20 deployments, compare them with the same labels, and produce the same expected output. If a result changed, I could trace that difference to the code or metric definition instead of a changing source. The corpus also gave each reported number a direct path back to its supporting records.

![Image](https://nextwork.ai/refreshed_maroon_timid_jujube/uploads/f830ef72-575b-48d3-826a-8c99f0333e00_kkq5ec51)

### Identifying incident-triggered rework deploys in the corpus

The incident-triggered rework deployments were d-006, d-011, and d-015. The sealed source established this result by setting is_incident_triggered to true for those three IDs in sealed_labels.json. The remaining deployment records were not marked true under that field.

The deployment details matched the labels. For d-006, d-011, and d-015, commit_message began with hotfix:, while linked_issue_title began with INC-. These fields gave the classifier visible evidence that the deployments responded to incidents instead of planned work.

This distinction controlled the rework-rate calculation. Three qualifying deployments out of 20 produced the final 15.0% result. One wrong ID or an incorrect true count would have changed that metric. The sealed labels therefore served as the scoring authority, while the matching commit and issue text explained the classifications.

## Defining Five Metrics in SQL

### Step goals

I defined five delivery metrics in SQL: deployment frequency, change lead time, change fail rate, failed deployment recovery time, and rework rate. Each definition ran against the captured records through DuckDB, giving the scoreboard a repeatable calculation path instead of placing unexplained values directly into the HTML.

The SQL separated the metric logic from the presentation. The dashboard displayed the results, while the queries determined how deployments, timestamps, failures, recovery periods, and rework labels were counted.

This structure created an audit trail from each displayed number to its SQL rule and supporting rows. That path mattered because DORA results change when teams use different clocks, events, or failure criteria. Writing the rules down did not remove every limitation, but it made the measurement choices visible and repeatable for later review.

![Image](https://nextwork.ai/refreshed_maroon_timid_jujube/uploads/f830ef72-575b-48d3-826a-8c99f0333e00_qriabuaj)

### Why the clock definition changes the answer

The same 20 deployments produced lead-time results of 26.0h, 18.0h, and 1.0h depending on whether the clock began at commit, PR open, or merge. The dataset did not change. The answer changed because each starting event measured a different part of the delivery path.

DORA defines change lead time from code committed to code running in production. Under that definition, the result for this build was 26.0h. Starting at PR open excluded the earlier interval and produced 18.0h. Starting at merge removed more waiting and produced 1.0h.

The 1.0h value made delivery look faster, but it was not the same metric because it hid all time before merge. This showed why metric names alone are not enough for comparison. Teams must use the same starting event, ending event, and record rules before their results can be compared fairly.

## Running the AI Gate for Rework Rate

### Step goals

I classified 20 deployment records as either incident-triggered rework or planned work. The AI gate compared those predictions with the sealed labels before allowing the rework rate into the final scoreboard. This made classification accuracy a requirement for the metric instead of a note added after calculation.

The gate checked both record-level accuracy and the total incident-triggered count. The first check identified whether any deployment ID received the wrong value. The second confirmed that the predicted number of true labels matched the sealed number.

Both checks were required because matching row totals would not prove correct classification. Two files could each contain 20 rows while disagreeing on which three deployments represented incident response. The gate could therefore calculate or withhold the metric based on the evidence, preventing an unsupported rate from appearing as a validated result.

![Image](https://nextwork.ai/refreshed_maroon_timid_jujube/uploads/f830ef72-575b-48d3-826a-8c99f0333e00_38g1fo50)

### Gate outcome and accuracy score

The AI gate passed and did not withhold the rework metric. Classification accuracy was 20/20, meaning every deployment ID received the same incident-triggered value in the prediction and sealed source. No IDs were listed as incorrect.

The incident-triggered count also matched 3/3. This check confirmed that the classifier identified exactly three true records and that the sealed labels contained the same number. Passing both tests allowed the system to calculate the rework rate.

With 3 incident-triggered deployments among 20 total deployments, the rate was 15.0%. This result did not prove that the classifier would always work on other data. It proved that its labels matched the complete sealed source for this captured corpus. The metric was released because the record-level comparison and true-count check both supported it.

## Annotating the Readout and Closing the Folder

### Step goals

I reviewed scoreboard.html to confirm that every required metric appeared with its limitations and "cannot-prove" annotations. The HTML file was the final delivery artifact, so the review covered both the rendered numbers and the explanations needed to interpret them.

These annotations mattered because a correct calculation can still support a wrong conclusion when its source records or clock definitions remain hidden. The dashboard needed to state where the captured evidence supported a result and where missing information prevented a claim.

The scoreboard remained self-contained and offline. Its metrics, classifications, and notes could be viewed without calling a live service. Reviewing the file before closing the folder confirmed that the output carried the same measurement choices recorded in the SQL and decision documents. This preserved the evidence path from the captured records through the calculations and into the stakeholder-facing report.

![Image](https://nextwork.ai/refreshed_maroon_timid_jujube/uploads/f830ef72-575b-48d3-826a-8c99f0333e00_w4p28c1u)

### After-action review: AI gate findings

The gate passed with 20/20 classification accuracy and a matching incident-triggered count of 3/3. Because both checks passed, the system calculated the rework rate as 15.0% instead of withholding it. No deployment ID was labeled incorrectly.

The record patterns supported the result. Deployments connected to hotfix commits and INC- issues were classified as incident-triggered rework. Causal feature deployments were not. The AI output matched the sealed source across all 20 records.

The main lesson came from the second gate check. Comparing the number of rows would only prove that both sources contained 20 records. It would not prove that both contained three true flags or marked the same IDs. A 20-versus-20 comparison could hide a miss within the 3-versus-3 incident count. Checking the true flags and individual IDs closed that gap.

## Secret Mission: Measuring a Real Repository

![Image](https://nextwork.ai/refreshed_maroon_timid_jujube/uploads/f830ef72-575b-48d3-826a-8c99f0333e00_1ldtrqzb)

### Which metrics were measurable and what was missing

The real-repository collector printed deployment_frequency: 20, change_lead_time: 0.0, change_fail_rate: 20.0, and failed_deployment_recovery_time: 2.0. It also printed commit 0.0, pr_opened None, merged None, followed by AssertionError: change_lead_time 0.0 does not match reference 26.0. This terminal output recorded the run even if the lab form showed “No file chosen.”

Deployment frequency was measurable because the Git log supplied 20 commits. Commit lead time printed 0.0h only because deployment time was copied from commit time. PR-open and merge clocks were missing.

The repository did not track production incidents, so fail rate, recovery, and rework were not measurable from this export. The missing evidence was distinct deployment timestamps, PR and merge times, and production-incident records.

## Reflections and Key Takeaways

### Tools and concepts learned

I used Python to run the build logic, DuckDB to query the captured records, Git to inspect repository history, and a local AI client to classify deployments. Git supplied history, DuckDB calculated the defined metrics, Python connected the stages, and the AI client produced labels for the gate to verify.

I learned how to define DORA metrics in SQL and why each definition must state its clocks and record rules. I also used MADR decision records to preserve the reason for choosing captured data instead of live API calls.

The AI gate showed how a model result could be checked before affecting a reported metric. The real-repository test exposed another limit: a calculation cannot recover timestamps or incident links that the source never recorded. Clear definitions, verified labels, and complete inputs were all required for a result that could be defended.

### Time and challenges

This build took approximately 65 minutes. The hardest part was mapping my repository history to the required JSON schema. The captured corpus contained the fields needed by the scoreboard, but the repository export did not provide every timestamp or incident relationship expected by the metric definitions.

I had to determine which of the four timestamp clocks were available and represent missing values without stopping the script before it reported what it found. Commit history existed, but separate deployment, PR-open, and merge timestamps did not.

Copying commit time into deployment time allowed the record to process, but it produced a 0.0h lead time instead of the 26.0h reference. The AssertionError exposed that mismatch rather than allowing the placeholder value to pass. The challenge was preserving the boundary between a value the script could calculate and a metric the source could support.

### Looking ahead

I completed this build to understand how DORA metrics, SQL, and AI-assisted validation can measure software delivery performance. The captured corpus showed how fixed deployment and incident records could produce repeatable calculations. The AI gate checked the classification evidence before the rework rate entered the dashboard.

The real-repository run showed what must exist before the same method can operate on live history. Commit records supported deployment frequency, but they did not provide distinct deployment times, PR and merge clocks, or production-incident links. Without those fields, several metrics remained unproven.

My next step is to automate the collection pipeline for live production environments. A recurring system would need repository events, deployment timestamps, pull-request history, merge times, and incident records while preserving the same SQL definitions, gate checks, and documented limits.

---

*Built with [NextWork](https://nextwork.ai) - [View this project](https://nextwork.ai/projects/f830ef72-575b-48d3-826a-8c99f0333e00)*
