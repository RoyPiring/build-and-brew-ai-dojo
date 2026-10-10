<img src="https://cdn.prod.website-files.com/677c400686e724409a5a7409/6790ad949cf622dc8dcd9fe4_nextwork-logo-leather.svg" alt="NextWork" width="300" />

# Is Better Search Worth the Wait?

**Project Link:** [View Project](https://nextwork.ai/projects/19fd734a-92b9-46ee-a9e0-7128d527e645)

**Author:** Roy Piring: Cloud Platform Engineer | Build Master  
**Email:** rpiringhawaii@gmail.com

---

![Image](https://nextwork.ai/pineapple_king_overclocked_the_ai/uploads/19fd734a-92b9-46ee-a9e0-7128d527e645_0rmq5iod)

## Defining an Evidence-Backed Retrieval Review

### Problem, four-row comparison, and evidence limits

I built a local retrieval benchmark to compare dense, sparse, hybrid, and hybrid-plus-rerank search against human SciFact relevance labels. Each method used the same 20 queries, corpus, identifiers, and scoring functions. The review measured Recall@5, nDCG@5, and rerank latency so quality changes could be considered beside the CPU time required to produce them.

I limited the conclusion to evidence from the completed 20-query run. The benchmark did not evaluate clinical guidelines, generated answers, or the full 300-query test. Any quality or latency claim not produced by the measured run remained an owed task rather than a reported result. This boundary kept the final decision tied to completed evidence and prevented an unfinished full-scale run from being presented as though it had occurred.

## Preparing a Local Benchmark for Timed Evaluation

### Setting up the local evaluation environment

I created a local repository, initialized a Python virtual environment, and installed the dependencies required to run dense, sparse, fused, and reranked retrieval on the CPU. I downloaded the embedding model, cross-encoder, and SciFact files before starting implementation. This gave the benchmark a controlled package set, model set, dataset location, and execution environment.

The setup supported a direct comparison among all four retrieval stages without changing machines or data sources between runs. Keeping the assets local also removed network response time from the measured search path after download. The repository held the design records, scorer, retrieval code, validation output, and Git history needed to explain how each result was produced and whether the implementation matched the frozen plan.

![Image](https://nextwork.ai/pineapple_king_overclocked_the_ai/uploads/19fd734a-92b9-46ee-a9e0-7128d527e645_4xd3r462)

### Evidence that data, models, and packages are ready

I confirmed that the repository and active .venv were available with Sentence Transformers 6.1.0, CPU PyTorch 2.14.1, and bm25s 0.3.13 installed. These versions established the local software baseline used by the scorer and retrieval stages. The environment was prepared before evaluation so package changes would not become an uncontrolled difference between methods.

I also confirmed that BAAI/bge-small-en-v1.5 and BAAI/bge-reranker-v2-m3 were cached locally. SciFact was extracted under data/scifact with corpus.jsonl, queries.jsonl, and qrels/test.tsv. Together, those checks showed that the corpus, queries, human labels, embedding model, and reranker were available before the timed comparison began. The evidence tied later metrics to one known local dataset and toolchain.

## Freezing the Design Decisions Before Building

### Creating an auditable solution contract

I documented the benchmark design before implementing the retrieval methods. The contract fixed the four comparison rows, human-label source, sample size, metrics, fusion rule, rerank boundary, and evidence limits. This created a stable basis for judging the build instead of allowing the evaluation rules to change after the scores or latency became visible.

The design records also separated measured facts from future work. The 20-query run was the accepted release scope, while the 300-query run remained optional until completed. By committing the documents first, I created Git evidence that the comparison method preceded implementation. Later code, results, and claims could therefore be checked against decisions recorded before execution rather than justified after seeing which method performed best.

![Image](https://nextwork.ai/pineapple_king_overclocked_the_ai/uploads/19fd734a-92b9-46ee-a9e0-7128d527e645_7zjp8swi)

### Auditing choices, alternatives, costs, and reversal triggers

I ran git log -1 --oneline and recorded 0d2695a Add project docs, confirming that the design records were committed before implementation. Record 1 required dense retrieval to be scored alone and named the three metrics that later results had to fill. This protected the baseline from being hidden by a stronger combined method after evaluation.

Record 2 fixed hybrid fusion as reciprocal rank fusion with constant 60 and limited the cross-encoder to the same 50 candidates, preventing reranking from adding unseen documents. Record 3 selected bm25s for exact sparse search and excluded generated answers. These decisions made the comparison easier to audit because the candidate boundary, fusion rule, sparse library, measured costs, and reversal conditions were known before the final review.

## Establishing Trustworthy Metrics and Human Labels

### Building the scorer and SciFact label loader

I implemented the metric functions and SciFact loader before comparing retrieval methods. The scorer calculated Recall@5 and nDCG@5 from ranked document identifiers and human relevance labels. The loader read the corpus, queries, and qrels/test.tsv, then returned the shared structures required by dense, sparse, hybrid, and reranked search.

This established one executable definition for every reported quality score. Centralizing the loader prevented individual methods from reading different samples or interpreting identifiers differently. Centralizing the metrics prevented each comparison row from using its own formula. The resulting baseline connected every score to the same human labels, cutoff, relevance mapping, and denominator logic, making differences attributable to retrieval behavior rather than separate evaluation code.

![Image](https://nextwork.ai/pineapple_king_overclocked_the_ai/uploads/19fd734a-92b9-46ee-a9e0-7128d527e645_sqwuhzti)

### Keeping every search method on the same benchmark

The load() function returned one corpus, one query list, and one relevance-label map, and all four search methods consumed that same return value. Document and query identifiers were converted to strings so dense results, sparse results, fused candidates, reranked output, and SciFact labels compared the same identifier type.

The evaluation sample contained the first 20 queries in first-seen label order. That ordering and sample were fixed before scoring, so no method received a different or more favorable question set. Each row was measured against the same documents, labels, cutoff, and metric functions. This common benchmark made the signed differences meaningful because a change in score reflected a changed ranking method rather than a changed sample, identifier format, or relevance source.

## Building and Stress-Testing Four Search Stages

### Implementing dense, sparse, hybrid, and reranked retrieval

I built dense retrieval with BAAI/bge-small-en-v1.5 to establish the semantic-search baseline. Sparse retrieval used bm25s for exact term matching. Each method returned its top 50 candidates. Hybrid retrieval joined those lists with reciprocal rank fusion using constant 60, preserving a declared and repeatable rule for combining the two ranking signals.

The final stage applied BAAI/bge-reranker-v2-m3 only to the same 50 hybrid candidates and reordered them with the cross-encoder. It did not introduce documents outside the fused set. This four-stage path isolated what each addition changed: sparse lexical evidence, fusion of dense and sparse ranks, and deeper scoring of a fixed candidate pool. Every method was then evaluated with the same 20 SciFact queries and human labels.

![Image](https://nextwork.ai/pineapple_king_overclocked_the_ai/uploads/19fd734a-92b9-46ee-a9e0-7128d527e645_wwif3cu5)

### Using an intentional metric failure to verify the guardrail

I deliberately broke the nDCG calculation to verify that the metric fixture could reject an incorrect denominator. The fixture expected 0.4982 but received 0.6509, producing the required failure. This showed that a faulty calculation could not silently appear as a quality gain merely because it generated a larger number.

I saved the failed result in check-out.txt while leaving the broken line uncommitted. That separation preserved evidence of the guardrail test without replacing the accepted scorer in Git. The later retrieval scores were considered trustworthy only after the correct line was restored and the same fixture passed. This sequence established that the metric check could fail when it should, not only report success under the final implementation.

## Measuring the Reranker and Publishing the Review

### Evaluating quality, signed changes, and rerank latency

I evaluated dense, sparse, hybrid, and hybrid-plus-rerank retrieval against the same human SciFact labels. Each row recorded Recall@5 and nDCG@5, while the reranked row also captured CPU timing. I calculated signed changes against dense so the review showed the direction of each difference rather than describing any changed score as an automatic gain.

The evidence was intended to answer a practical question: whether the reranker’s measured quality change justified its additional CPU cost. The completed run showed that hybrid plus rerank lost Recall@5 against dense while gaining nDCG@5. Its 95th-percentile latency was 60,388.1236 milliseconds. Because the two quality changes did not both remain positive and the waiting cost was large, the measured result did not support adopting the CPU reranker for this release.

### Restoring the scorer while preserving failure evidence

I restored the correct nDCG denominator as min(k, len(relevant)) and reran the metric fixture. The scorer returned the expected 0.4982 and reported ALL CHECKS PASS. This confirmed that the accepted metric code once again matched the known fixture after the deliberate failure had tested the guardrail.

check-out.txt retained both outcomes: the caught 0.6509 failure and the later passing result. Running git diff -- metrics.py returned no output, proving that the working scorer matched the committed passing version. This evidence kept the failed experiment visible without leaving the benchmark in a broken state. It also tied the published evaluation rows to code that passed the same check used to reject the incorrect formula.

### Removing unsupported claims through trace scoring

I trace-scored the close-out materials and found 3 untraceable claims among 88 reviewed claims. Rather than leaving them as unsupported explanations, I removed them from the affected documents. The teach-back dropped two method phrases that were not supported by the frozen design records, keeping its description within the implemented and measured retrieval path.

The after-action review also stopped claiming that the measured lift surprised the learner because no retained evidence established that reaction. The single-page readout required no change. I preserved the first-draft score as 3/88 rather than rewriting the audit history after correction. This process distinguished the original trace result from the cleaned artifacts and ensured that the final narrative did not state more than the repository evidence supported.

## Testing Whether Reranker Lift Holds at Full Scale

![Image](https://nextwork.ai/pineapple_king_overclocked_the_ai/uploads/19fd734a-92b9-46ee-a9e0-7128d527e645_i6cbdqp7)

### Comparing full-test signed changes against zero

I did not finish the 300-query evaluation because the CPU rerank was estimated to require about 4.5 to 5 hours. The release therefore used the completed 20-query measurements. I did not invent a 30-query result or present the unfinished full test as evidence. The larger evaluation remained an owed task whose outcome could later confirm or reverse the limited release decision.

On the 20-query run, hybrid plus rerank versus dense changed Recall@5 by -0.025000000000000022 and nDCG@5 by 0.07226364609613845. Recall moved below zero while nDCG moved above zero, so the signed changes were not both positive. This mixed result, combined with the measured CPU latency, meant the completed evidence did not show a clear quality win that justified the additional waiting time.

## Reflecting on the Retrieval Engineering Journey

### Tools and concepts developed

I used CPU PyTorch, Sentence Transformers, bm25s, SciFact labels, Git, and the v1.0-rerank tag. Dense and sparse search each returned 50 candidates, reciprocal rank fusion combined them with constant 60, and the cross-encoder reordered only that same set. This preserved a clear boundary between candidate retrieval and reranking.

The 20-query evaluation taught me to compare Recall@5, nDCG@5, and p95 latency together rather than treating one higher metric as the entire decision. Hybrid plus rerank lost Recall@5 against dense, gained nDCG@5, and reached a p95 time of 60,388.1236 milliseconds. Under the declared decision rule, that mixed quality result did not repay the CPU time. The conclusion applied only to the measured sample and local environment.

### Time investment and key challenge

The completed 20-query release through the v1.0-rerank tag took approximately one hour. That time covered the design documents, metric scorer, SciFact loader, four retrieval stages, deliberate metric failure, restored validation, measured evaluation, trace scoring, and final readout. It did not include a completed full-test rerank.

The main challenge was keeping the release decision separate from the unfinished 300-query run. That larger evaluation required an estimated 4.5 to 5 hours, so it remained outside the one-hour release and could not support its conclusion. I handled the limit by publishing the actual 20-query evidence, retaining the full run as future work, and refusing to replace missing measurements with estimates or invented intermediate results.

### Next learning goal

I completed this work to learn how to judge a CPU reranker with measured Recall@5, nDCG@5, and p95 latency while keeping every claim tied to repository evidence. I also practiced fixing the sample and candidate boundaries, validating the scorer through an intentional failure, calculating signed changes, and removing statements that could not be traced to the design or results.

My next goal is to complete the full 300-query SciFact test without allowing rerank time to hide a weak quality change. I want to retain per-query timing and scores, support safe continuation if execution stops, and compare the final signed changes with zero under the same contract. That evidence can then show whether the limited 20-query conclusion holds at full-test scale.

---

*Built with [NextWork](https://nextwork.ai) - [View this project](https://nextwork.ai/projects/19fd734a-92b9-46ee-a9e0-7128d527e645)*
