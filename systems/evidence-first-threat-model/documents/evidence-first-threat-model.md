<img src="https://cdn.prod.website-files.com/677c400686e724409a5a7409/6790ad949cf622dc8dcd9fe4_nextwork-logo-leather.svg" alt="NextWork" width="300" />

# The Diagram That Argues Back

**Project Link:** [View Project](https://nextwork.ai/projects/a2a423fa-2a92-4f6e-be1b-74b006921524)

**Author:** Roy Piring: Cloud Platform Engineer | Build Master  
**Email:** rpiringhawaii@gmail.com

---

![Image](https://nextwork.ai/refreshed_maroon_timid_jujube/uploads/a2a423fa-2a92-4f6e-be1b-74b006921524_k22py10o)

## Committing to an Evidence-First Threat Model

### Answering the PM with ordered local evidence

I built this threat model to answer the product manager with evidence that could be checked without relying on my explanation. The final readout needed to show which design claims survived analysis, which predictions matched generated findings, and which residual risks still required ownership. I treated evidence order as part of the result because predictions written after tool output would not test prior judgment.

Before running pytm, I documented the product claims, architecture, STRIDE predictions, and validation rules inside the local repository. I committed those files before generating out.json, dfd.dot, or any ownership register. This created a visible separation between what I expected and what the tool later reported. Git history supported that sequence, although local history could still be rewritten. The final conclusion therefore relied on both the commit order and the retained files rather than presenting a timestamp alone as absolute proof.

## Preparing a Local, Auditable Evidence Repository

### Setting up the local evidence chain

I initialized a local Git repository and created the workspace structure needed for design files, generated findings, validation results, and the final readout. I added .gitignore rules for the virtual environment and temporary outputs, then created TASKS.md to track the required build stages. This established one location where every claim could be connected to a committed source or generated artifact.

I kept the repository local so no remote service could alter the evidence order or introduce unrelated activity during the build. The first commit captured the setup before I wrote the design assumptions or ran the threat-modeling tool. Later commits separated predictions, findings, ownership decisions, and reporting. This structure did not make the repository tamper-proof, but it made the work inspectable. A reviewer could follow the sequence, compare file creation points, and identify whether any generated evidence appeared before the documented predictions.

![Image](https://nextwork.ai/refreshed_maroon_timid_jujube/uploads/a2a423fa-2a92-4f6e-be1b-74b006921524_xoxppebx)

### Verifying repository readiness

I created a project-specific virtual environment running Python 3.13.13 and verified that python -c "import pytm" completed silently with exit code 0. That check showed that pytm 1.4.0 was installed, isolated from the system interpreter, and loadable before the model was executed. It did not prove that the later model code was correct, only that the required package could be imported.

I also confirmed that repository-local user.name and user.email values were configured. This allowed Git to record commits without changing identity settings for other repositories. After the initial commit, git status was clean on main, and git remote -v returned no entries. Those results established that the workspace was ready for the design phase and that its evidence chain remained local. They did not prove immutability, so the retained commit sequence and artifact contents still needed independent review.

## Committing PM Claims Before Tool Output

### Documenting design assumptions and predictions

I documented the product requirements, system architecture, trust boundaries, data flows, PM claims, and initial STRIDE predictions before running pytm. Seven design files captured what the system was expected to do and which threats I believed would apply. Each prediction used a stable identifier so it could later be compared with generated findings rather than matched through vague descriptions.

The prediction set included YES rows for expected pytm findings and LOCAL rows for risks I believed mattered even if pytm 1.4.0 did not generate them. This preserved security concerns outside the tool’s built-in rules instead of treating absence from output as proof of safety. I committed the design state before creating out.json or dfd.dot. That separation turned the later comparison into a genuine test of the predictions. It also made unmatched rows visible, requiring classification and ownership rather than allowing them to disappear from the final report.

![Image](https://nextwork.ai/refreshed_maroon_timid_jujube/uploads/a2a423fa-2a92-4f6e-be1b-74b006921524_d0ddzvpi)

### Proving predictions came before findings

The evidence showed that all seven design files and the prediction set existed while out.json and dfd.dot were still absent. Commit 5434c85 then recorded that design state before any pytm execution. The generated files appeared only afterward, creating a clear ordering between the documented expectations and the tool-produced findings.

This sequence mattered because predictions written after reviewing output would measure transcription rather than security judgment. The commit prevented ordinary editing from silently changing the recorded expectations without producing a visible history change. It did not make the claim cryptographically final because a local repository owner could rewrite commits. Stronger proof would require a signed commit, protected remote branch, or independent timestamp. Within this build, the retained files, commit identifier, and absence of generated outputs at prediction time provided the evidence that predictions preceded findings.

## Building the Threat Model and Preserving the Ownership Gap

### Creating a claim-traced pytm model

I converted the product manager’s requirements into a pytm 1.4.0 model containing the system actors, processes, data stores, data flows, trust boundaries, and claim-linked properties. Each modeled element traced back to a documented PM claim so generated findings could be connected to the design statement that caused them. The resulting model produced the data-flow diagram and structured threat output used by the validator.

I intentionally ran the model before creating threat-register.md. This preserved the gap between detecting a threat and assigning someone to make a decision about it. If ownership had been created automatically with the findings, the system could appear complete without proving that each result had been reviewed. The initial validation therefore measured the incomplete state on purpose. Generated findings established exposure, while the missing register demonstrated that exposure alone did not create accountability, acceptance, mitigation, or follow-up.

![Image](https://nextwork.ai/refreshed_maroon_timid_jujube/uploads/a2a423fa-2a92-4f6e-be1b-74b006921524_ypcmd7yd)

### Understanding the required ownership failure

The first validation run failed because threat-register.md did not yet exist. Check 3 found that generated findings had no register rows containing allowed decisions and named owners. Check 6 also found unmatched predictions without owned ASK rows. These failures were expected because the model had generated evidence before the implementation assigned accountability.

check.py returned exit code 1 whenever any of its six checks failed, so the result could not be mistaken for a warning-only state. This was the required must-fail proof: the pipeline refused to treat identified threats as governed until ownership and decisions were recorded. The failure also showed that prediction quality and ownership were separate controls. A predicted threat could still lack an accountable owner, while an unmatched prediction still required classification. Passing required both complete findings coverage and explicit handling of gaps outside pytm’s output.

## Turning Findings into Owned Decisions

### Validating findings, ownership, and prediction quality

I created the threat register and assigned every generated finding an allowed decision and named owner. High and Very High findings also required explicit product-manager acceptance, which prevented severe risks from being closed only through an engineering entry. Unmatched predictions received owned ASK rows so risks outside pytm 1.4.0 remained visible and actionable.

The second execution of check.py passed all six checks and returned exit code 0. Ticket Database produced four findings, while Ticket Summariser produced five. Every finding had a permitted decision and owner, and every High or Very High item had PM acceptance. The validator also measured prediction recall and the unmatched rate instead of reporting only a binary pass. This made the final evidence cover three questions at once: whether the model produced traceable findings, whether every result had ownership, and whether my original predictions matched the generated threat set.

![Image](https://nextwork.ai/refreshed_maroon_timid_jujube/uploads/a2a423fa-2a92-4f6e-be1b-74b006921524_ntc37u63)

### Interpreting recall and unmatched predictions

The validator reported recall of 7/9, or 77.8%. This meant seven of the nine pytm findings were already represented by predictions marked YES. The remaining two findings were tool-generated results that I had not predicted. The score showed that the design review anticipated most generated threats but still benefited from automated analysis.

The unmatched-prediction rate was 3/10, or 30.0%. Three of the ten predicted identifiers did not appear in pytm output. I classified those rows as LOCAL because they represented concerns outside pytm 1.4.0 rather than proven false predictions. Each received an owned ASK entry so the gap remained visible. Together, the fractions showed that the predictions were useful but incomplete in both directions. The tool found issues I missed, while my local review preserved risks the tool did not express. Neither source was treated as complete by itself.

![Image](https://nextwork.ai/refreshed_maroon_timid_jujube/uploads/a2a423fa-2a92-4f6e-be1b-74b006921524_24g1xz23)

## Testing Claim Sensitivity and Shipping the Readout

### Comparing findings after the third-party claim flip

I tested whether a specific PM claim materially affected the generated threat model by changing summariser.isThirdParty from True to False in an isolated comparison. The modified run produced one fewer finding and added no new findings. The removed result was Ticket Summariser | LLM03 | High, which described third-party leakage of personal data.

That change showed that LLM03 depended on PM-5, the design claim identifying the summariser as third party. The finding was therefore traceable to a declared property rather than appearing as an unexplained tool judgment. I preserved the original out.json unchanged and stored the comparison separately so the sensitivity test did not overwrite the graded evidence. The result did not prove that first-party processing removed every privacy risk. It proved only that this specific pytm finding was sensitive to the third-party claim and disappeared when that claim changed.

## Reflecting on the Evidence Workflow

### Tools and threat-modeling concepts applied

I used pytm 1.4.0 to generate STRIDE findings and a data-flow diagram, Git to preserve the evidence order, Python to run the six validation checks, and a self-contained HTML readout for offline review. Each tool served a different role: pytm generated findings, Git recorded sequence, the validator enforced ownership rules, and the readout joined the evidence into one review surface.

The main concept was design-first threat modeling. I documented PM claims and predictions before tool execution, then compared those expectations with generated results. I also separated detection from governance by requiring a threat register, named owners, allowed decisions, and PM acceptance for severe findings. The sensitivity test showed how one design claim could alter output. Together, these controls reduced untraceable conclusions and made disagreements visible rather than allowing the diagram or the tool to act as unquestioned authority.

### Project duration and challenge

I completed the build in 55 minutes. The hardest part was ensuring that every statement in the final readout traced to a local design artifact, generated result, register row, validation check, or sensitivity comparison. A visually convincing report would not have been enough if a reviewer could not locate the evidence supporting each conclusion.

The ownership gap also required careful handling. I needed the first validation run to fail because no threat register existed, then pass only after findings and unmatched predictions received decisions and owners. If I had created the register too early, the build would have lost its must-fail proof. I also preserved the original out.json during the claim-flip experiment so the comparison did not rewrite the graded baseline. These constraints turned the threat model into an auditable decision process instead of a diagram generated once and accepted without challenge.

### Next steps in security engineering

I completed this build to learn how to produce an audited, evidence-led threat model whose claims could be checked without rerunning the entire workflow. The final package preserved design assumptions, STRIDE predictions, pytm findings, ownership decisions, validation results, sensitivity evidence, and an offline readout. The six-check validator passed only after every required accountability condition was satisfied.

My next step is to run the same checks inside CI/CD. The pipeline should reject changes that introduce unowned findings, remove required PM acceptance, lower traceability, or leave unmatched predictions without classified ASK rows. It should also preserve design predictions before generating new findings so automated execution does not erase evidence order. A future extension could compare model revisions across commits and require review when a changed design claim adds or removes a High or Very High threat.

---

*Built with [NextWork](https://nextwork.ai) - [View this project](https://nextwork.ai/projects/a2a423fa-2a92-4f6e-be1b-74b006921524)*
