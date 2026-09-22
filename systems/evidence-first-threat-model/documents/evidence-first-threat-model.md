<img src="https://cdn.prod.website-files.com/677c400686e724409a5a7409/6790ad949cf622dc8dcd9fe4_nextwork-logo-leather.svg" alt="NextWork" width="300" />

# The Diagram That Argues Back

**Project Link:** [View Project](https://nextwork.ai/projects/a2a423fa-2a92-4f6e-be1b-74b006921524)

**Author:** Roy Piring: Cloud Platform Engineer | Build Master  
**Email:** rpiringhawaii@gmail.com

---

![Image](https://nextwork.ai/refreshed_maroon_timid_jujube/uploads/a2a423fa-2a92-4f6e-be1b-74b006921524_k22py10o)

## Establishing an Evidence-First Investigation

### Protecting the order of evidence

I built this investigation so the threat-model findings could challenge the product design without rewriting what I originally expected. I first initialized a local Git repository and froze the product manager’s design with twelve upfront STRIDE predictions. Only after that commit did I allow pytm to generate findings or create the data-flow diagram.

I translated the AI ticket summariser architecture, components, trust boundaries, and data flows into Python using pytm 1.4.0. I then compared the automated findings with the prediction set, assigned decisions and owners, and exported an offline HTML evidence pack. This ordering separated prior judgment from later tool output. It did not make Git history immutable, but it made changes inspectable. The resulting evidence showed what I predicted, what pytm reported, where the two disagreed, and which residual risks required an accountable decision.

## Creating a Timestamped Evidence Repository

### Setting up the local repository

I initialized a local Git repository before creating the threat model or its generated findings. I configured repository-specific author details, created the workspace structure, and added ignore rules for temporary files and the Python virtual environment. This kept the evidence history isolated from unrelated repositories and prevented generated or environment-specific files from entering the baseline accidentally.

I created the virtual environment and installed pytm 1.4.0 inside it. Import validation confirmed the package was loadable before I wrote the model. The repository had no remote, so the work remained local during the investigation. I used commits to separate setup, design, predictions, model outputs, validator failure, ownership decisions, and final reporting. This sequence created a timestamped audit trail, although a local owner could still rewrite it. Stronger preservation would require signed commits, protected remote history, or an independent timestamp service.

![Image](https://nextwork.ai/refreshed_maroon_timid_jujube/uploads/a2a423fa-2a92-4f6e-be1b-74b006921524_ijawa3dm)

### Proving the repository existed first

The baseline check showed that the repository and committed design state existed before pytm produced any findings. All seven design documents and the upfront STRIDE predictions were present, while out.json and dfd.dot were absent. This established a visible boundary between human expectations and automated output.

Committing that state prevented ordinary edits from silently changing the predictions after the findings appeared. A later reviewer could compare the frozen design commit with the generated artifacts and identify any subsequent changes. The evidence supported prediction independence because the expected threats existed while the tool outputs did not. It did not make influence logically impossible, since local Git history can be rewritten and similar pytm rules could have been known beforehand. Within this investigation, the retained commit sequence and file-presence checks provided the chronological baseline required for the design-first claim.

## Documenting Predictions Before Results

### Recording the PM design and predictions

I documented the product manager’s design claims, system components, trust boundaries, data flows, and security assumptions before building the pytm model. Seven design files preserved the requirements that later model properties needed to represent. This prevented the Python implementation from becoming the only record of what the product was supposed to do.

I also recorded the upfront STRIDE predictions with stable identifiers so they could be compared against generated findings without relying on approximate wording. The predictions expressed my expected threats, while local rows preserved concerns that pytm might not generate. I committed the design and prediction set before executing the model. This made later disagreement useful evidence rather than something to hide. A matched row supported prediction recall, a missed finding exposed a gap in my review, and an unmatched prediction identified a concern that required classification even when it remained outside the tool’s reported findings.

![Image](https://nextwork.ai/refreshed_maroon_timid_jujube/uploads/a2a423fa-2a92-4f6e-be1b-74b006921524_d0ddzvpi)

### Verifying predictions preceded outputs

The pre-run verification returned True for the presence of all seven design files and the recorded STRIDE predictions. It returned False for out.json and dfd.dot, confirming that neither automated findings nor the generated diagram existed when the check ran. I preserved this result with the design commit.

This evidence established an objective file-order boundary: the requirements and predictions were present before pytm generated its outputs. That mattered because predictions written after reviewing out.json would measure transcription or hindsight instead of prior security judgment. The check did not prove that I had no prior knowledge of pytm’s rules, and local history could still be rewritten. It demonstrated the narrower claim required by the investigation: the committed prediction artifacts preceded the generated model files. The later recall and unmatched-rate calculations therefore compared two separately recorded states rather than two documents created from the same output.

## Building the Model and Capturing Its Initial Failure

### Building a testable model

I translated the documented design claims into a Python model containing the AI ticket summariser’s actors, processes, data stores, data flows, trust boundaries, and claim-sensitive properties. Pytm generated dfd.dot for the data-flow diagram and out.json for the structured threat findings. Each modeled property traced back to the written design so findings could be connected to their triggering claim.

I then ran the validator while deliberately leaving threat-register.md absent. This created the required failing baseline before any ownership decisions existed. The run compared predictions with findings but could not approve the investigation because the findings lacked owners and recorded treatment decisions. I committed the failure evidence before creating the register. This sequence showed that tool output alone was insufficient. The model could identify threats, but the investigation remained incomplete until people accepted responsibility for evaluating, mitigating, accepting, or escalating each finding.

![Image](https://nextwork.ai/refreshed_maroon_timid_jujube/uploads/a2a423fa-2a92-4f6e-be1b-74b006921524_ypcmd7yd)

### Interpreting the expected validator failure

The initial validator run failed because threat-register.md was intentionally absent. Without the register, the generated findings had no named owners, allowed decisions, mitigation records, or documented acceptance. Unmatched predictions also lacked classified and owned ASK rows. The missing file therefore represented an accountability failure rather than a model-generation error.

check.py returned a non-zero status when any required check failed, preventing the pipeline from presenting the incomplete state as successful. I committed this result before adding the ownership records. That must-fail proof established that the later passing state came from deliberate work rather than a validator that always returned success. The failure did not mean the design was insecure by itself. It showed that identified threats had not yet been governed. Detection created evidence of risk, while the threat register connected each result to a decision-maker and recorded response.

## Validating Findings and Proving Prediction Order

### Validating the investigation findings

I created the threat register and added a row for every generated finding. Each row contained an allowed decision and named owner, while High and Very High findings required explicit product-manager acceptance. Predictions that did not match pytm output received classified, owned ASK rows so concerns outside the tool’s rule set remained visible.

I reran check.py after completing the register. All six checks passed, and the process returned EXIT: 0. The passing result confirmed that the expected component findings existed, every finding had an acceptable decision and owner, severe findings had PM acceptance, recall was calculated, and unmatched predictions were classified. Git history still showed that the original predictions preceded out.json, dfd.dot, and the threat register. The completed validation therefore joined three forms of evidence: chronological order, automated findings, and accountable human decisions. None of those elements alone would have provided the same audit trail.

![Image](https://nextwork.ai/refreshed_maroon_timid_jujube/uploads/a2a423fa-2a92-4f6e-be1b-74b006921524_ntc37u63)

### Interpreting recall and unmatched-rate results

The validator reported recall of 7/9, or 77.8%. Seven of the nine threats generated by the scored pytm output matched my predictions, while two findings were not anticipated. This showed that the design review identified most of the tool-reported threats but did not make automated modeling redundant.

The unmatched rate was 3/10, or 30.0%. Three of the ten scored prediction identifiers did not correspond to a pytm finding. I retained those concerns as classified, owned rows rather than deleting them to raise the score. Together, the fractions showed disagreement in both directions: pytm found two items I missed, while my review identified three concerns the tool did not report. The predictions were useful but incomplete, and the automated results were informative but not exhaustive. Assigning the gaps to owned ASK decisions prevented either set from being treated as the sole authority.

![Image](https://nextwork.ai/refreshed_maroon_timid_jujube/uploads/a2a423fa-2a92-4f6e-be1b-74b006921524_24g1xz23)

## Testing How a Third-Party Claim Changes the Findings

### Assessing the impact of the claim flip

I tested claim sensitivity by changing summariser.isThirdParty from True to False in an isolated model run. The original out.json contained 126 findings, while out-flipped.json contained 125. The modified run removed one finding and introduced no new findings. Every other result retained the same target, threat identifier, severity, and text.

The removed finding was Ticket Summariser | LLM03 | High | Sensitive Data Leakage to Third-Party Provider. Its rule required isThirdParty to be True, so changing that claim removed the condition that produced it. I preserved the original output and stored the flipped result separately to avoid rewriting the graded evidence. The experiment showed that this specific finding traced to the third-party design claim. It did not prove that first-party processing eliminated sensitive-data leakage or made the summariser safe. Other threats remained unchanged and still required their recorded decisions and owners.

## Reflecting on Evidence-Based Investigation Skills

### Tools and concepts learned

I used pytm 1.4.0 to create the programmatic threat model and generate findings, Git to preserve the sequence of evidence, and Python to execute the model and six validation checks. I also produced a data-flow diagram and an offline HTML evidence pack so reviewers could inspect the design, predictions, findings, ownership decisions, and claim-flip comparison without rerunning the build.

The main lesson was that evidence order affected the credibility of a threat model. Requirements and predictions needed to exist before automated findings if recall was intended to measure prior judgment. I also learned that detection did not create accountability. The must-fail run proved that findings without owners and decisions were incomplete. Recall, unmatched predictions, and claim sensitivity exposed different limitations in both human and automated analysis. Together, these controls made the investigation traceable without treating pytm or the diagram as unquestionable authority.

### Project timeline and challenges

I completed the build in approximately 65 minutes. The most difficult part was keeping threat-register.md synchronized with the findings in out.json. Minor differences in threat identifiers, target names, or decision formatting caused validator failures even when the intended ownership appeared correct.

I resolved those discrepancies by comparing the structured output with the register rows and correcting the mappings without changing the original predictions. I also preserved a clean Git sequence: design and predictions first, generated output second, expected validation failure third, and owned passing state afterward. That order required care because updating a file in the wrong stage could weaken the evidence-first claim. The challenge showed why stable identifiers and automated checks mattered. Manual review could miss a small mismatch, while the validator forced every generated result and unmatched prediction into an explicit, traceable state.

### Continuing the learning journey

I completed this build to learn how to create an accountable, design-first threat model with a visible evidence chain from initial claims to audited decisions. The finished package preserved the PM design, STRIDE predictions, pytm outputs, expected failure, threat register, passing validation, claim-sensitivity test, and offline readout.

My next step is to place the validation workflow inside CI/CD. The pipeline should reject changes that introduce an unowned finding, remove required PM acceptance, leave an unmatched prediction without an ASK owner, or generate outputs before the prediction baseline is approved. I also want to compare threat-model changes across commits so a new or removed High finding requires review. Automation should enforce the evidence contract without silently choosing risk decisions. Ownership, acceptance, and interpretation must remain visible human responsibilities supported by repeatable technical checks.

---

*Built with [NextWork](https://nextwork.ai) - [View this project](https://nextwork.ai/projects/a2a423fa-2a92-4f6e-be1b-74b006921524)*
