<img src="https://cdn.prod.website-files.com/677c400686e724409a5a7409/6790ad949cf622dc8dcd9fe4_nextwork-logo-leather.svg" alt="NextWork" width="300" />

# Terraform Module Testing and Decisions

**Project Link:** [View Project](https://nextwork.ai/projects/fbf2e057-439b-4ad8-bb1d-fd11ab79900e)

**Author:** Roy Piring Jr: Sr. Cloud Engineer | Architect  
**Email:** rpiringhawaii@gmail.com

---

![Image](https://nextwork.ai/refreshed_maroon_timid_jujube/uploads/fbf2e057-439b-4ad8-bb1d-fd11ab79900e_ppwrf5ty)

## Building a Provable Network Module with Documented Decisions

### Project purpose and why decision documentation matters

I built a defensible Terraform network module with decision records that explain why each major architecture choice was made. The goal was to leave future engineers more than working code. They also needed the reasoning, trade-offs, and conditions that would justify changing a decision later.

That mattered because undocumented choices tend to get re-litigated or copied without context. The ADRs gave the module a durable record of what was selected, what alternatives were rejected, and what evidence would cause the choice to be revisited.

The result was a build where infrastructure reliability and decision quality could both be reviewed instead of inferred.

## Setting Up the Development Environment and Identifying the Seeded Bug

### Verifying the toolchain and project board

In this step, I verified the local toolchain and prepared the GitHub delivery workflow for the Terraform network module. The goal was to make sure the environment, repository, and board were ready before I changed the module.

This setup created a controlled path for building, testing, documenting, and reviewing each change. It also gave the later red-to-green proof a clean delivery trail.

The toolchain checks mattered because a failed prerequisite can look like a module failure. Verifying the environment first separated setup problems from actual infrastructure logic defects.

![Image](https://nextwork.ai/refreshed_maroon_timid_jujube/uploads/fbf2e057-439b-4ad8-bb1d-fd11ab79900e_71o70q5v)

### Understanding the intentional flaw in variables.tf

vpc_cidr was declared with type = string but had no validation block. Terraform therefore checked only that the input was text, not whether that text represented a valid CIDR.

I verified the gap by planning with not-a-cidr-at-all. The plan completed with exit code 0, which meant the malformed value passed the module boundary and failed only later when the provider tried to use it.

Checkov did not catch the defect either because the problem lived at the input boundary, not inside a resource policy. That was exactly the gap a Terraform validation block needed to close.

## Designing Before Building: Requirements, ADRs, Diagram, and Plan

### Why design artifacts come before any code

In this step, I created the requirements brief, Architecture Decision Records, diagram, and build plan before changing the module logic.

The purpose was to make the implementation follow documented decisions instead of allowing the architecture to emerge from whatever code happened to be written first.

This gave the next engineer a record of the intended behavior, testing strategy, constraints, and delivery path. They could understand why the module looked the way it did without reconstructing the reasoning from Git history.

### Anatomy of an Architecture Decision Record

Each ADR used four sections: Decision, Alternative, Tradeoff, and Reversal Trigger. Together, they recorded the selected path, the option it beat, the cost accepted, and the observable condition that would cause the decision to change.

The reversal trigger was the forward-looking part. The other sections captured what was known when the decision was made, while the trigger defined the evidence that would make the decision wrong later.

In this build, three of the four reversal triggers described a constraint being removed: integration proof required, account available, and Bridgecrew API key available. That made the ADRs useful as both decision records and a record of what currently limited the architecture

## Drafting the Test Matrix with AI and Scoring the Results

### Using AI to draft five mock_provider test cases

In this step, I used AI to draft tests/network.tftest.hcl with five run blocks against mock_provider. The tests covered expected module behavior, CIDR arithmetic, and adversarial input designed to expose the missing validation rule.

The generated tests were not accepted blindly. I compared them against the answer key, ran the suite, and checked whether the assertions matched the module’s actual subnet calculations.

The most important requirement was that at least one test had to fail for the existing defect. The suite needed to prove the missing guard before I fixed it.

### What the failing malformed-CIDR test revealed

Case 5 proved that the module had no guard at its own boundary. The test expected var.vpc_cidr to reject not-a-cidr, but the module accepted it because type = string constrained only the value type, not its meaning.

The module failed late instead of failing closed. The malformed value travelled into aws_vpc.this.cidr_block, where the provider rejected it, while the CIDR calculation also produced an index error on the way through.

The test was valuable because it proved an absence. Nothing rejected the bad value where it entered the module, and that was the failure. Adding validation moved rejection to variable assignment and made Case 5 pass without changing the test.

## Fixing the Module, Passing All Tests, and Scanning Clean

### Adding the validation block to enforce fail-closed behavior

In this step, I added a validation block to vpc_cidr so malformed CIDR values were rejected at the module boundary.

The change moved failure from provider execution to variable assignment. Callers now received an error tied directly to the invalid input instead of a later failure inside a resource.

That was the fail-closed behavior the test matrix was designed to enforce. The module stopped accepting values it could not actually use.

### Red then green: what each Actions run proves

The story/2 red state contained Case 5 against main, where expect_failures = [var.vpc_cidr] required the variable to reject not-a-cidr. Because the validation block did not yet exist, Terraform reported Missing expected failure.

The story/3 green state added the validation block. vpc_cidr then rejected the malformed value during assignment, satisfied expect_failures, and turned Case 5 green.

The test file was identical between the two states. Only the module changed. In this run, neither merge itself triggered a workflow because .github/workflows/ did not exist yet, so I produced the red-to-green proof through a different run sequence.

## Proving the CI Gate, Defending Every Decision, and Closing the Board

### Gate proof, teach-back, and after-action review

In this step, I documented the CI/CD gate proof using the red and green commit SHAs from the GitHub Actions history.

The goal was to leave evidence that the pipeline could detect a real module defect, show the exact fix that changed the result, and connect that proof back to the decision records and test strategy.

The teach-back and after-action review then captured what the gate proved, where the workflow differed from the original plan, and what should change in the next build.

![Image](https://nextwork.ai/refreshed_maroon_timid_jujube/uploads/fbf2e057-439b-4ad8-bb1d-fd11ab79900e_jdkhm2l7)

### What the red-to-green transition demonstrates

The red-to-green transition proved the gate caught a real missing guard. The test file stayed byte-identical while one validation block changed in variables.tf, so the failure came from the module defect rather than a changed assertion.

The gate ran terraform test and checkov -d . --framework terraform on pushes to main and pull requests. It used mock_provider, required no cloud account or stored credentials, and contained no metered AI step.

The proof also covered the defect class, not only one bad string. If a future change removes an input validation guard that the test expects, the same gate can fail again.

## Extending the Module: Additional Validation and Reversal Triggers

### Boundary added and conditions that would reverse the decision

I added a second validation block to availability_zones that capped the list at 6. Case 6, rejects_too_many_azs, proved that a 7-element list was rejected during variable assignment with A maximum of 6 availability zones is supported.

The reversal condition was also explicit. I would remove or change the cap if AWS introduced a region with more than 6 availability zones, or if the module were adapted to a non-AWS provider whose regions supported more than 6 zones.

The trigger was based on real region shape, not address-space limits. I verified that 7 zones on a /16 still calculated successfully and that the arithmetic limit was far higher, at roughly 65,536. The cap existed to catch likely input mistakes, not to protect subnet math.

## Reflections and Project Close-Out

### Key tools and concepts from the project

The key tools I used included Terraform’s test framework with run blocks, mock_provider, expect_failures, and cidrsubnet; Checkov with inline #checkov:skip=ID:reason; GitHub Actions for CI enforcement; and gh for pull requests and board work.

The main technical lesson was to fail closed at the variable boundary. type = string proves that an input is text, but a validation block proves whether that text is meaningful for the module. Writing the test before the fix also made the red run credible because it demonstrated an existing defect instead of a later assertion change.

The broader decision lesson was that a technical choice becomes more defensible when it includes a reversal trigger. Each ADR named an observable condition that would justify revisiting the choice. I also learned that a check that cannot fail is not meaningful proof. 

### Measured elapsed time and biggest challenge

This build took me approximately 55 minutes. The hardest part was getting the test matrix to match the module’s real subnetting behavior. The expected CIDR values described a different scheme than the one I initially wrote, so I had to rebuild the test file after it had already been committed.

The Checkov pass was close behind. It returned four failures instead of the clean result I expected, and each finding required a decision about whether to change the Terraform configuration or suppress the check with a named reason.

The most useful surprise was that a /12 VPC cannot exist in AWS. The case looked valid on paper and failed only when the suite ran, which reinforced the value of executing the system instead of trusting inspection alone.

### Learning goals and next skills to pursue

I completed this build to learn how to prove Terraform module behavior without a cloud account. The mocked test matrix caught both incorrect computed values and inputs that were never validated, and it could run quickly without AWS resources.

I also learned how to make infrastructure decisions easier to revisit. The four ADRs recorded the chosen path, rejected alternative, accepted trade-off, and the condition that would reverse each decision.

The next skill I want to build is real idempotence proof. ADR-3 deliberately left that claim unmade because mocks are deterministic but not stateful. Closing that gap would require applying against real state and then running a plan that proves no further changes are required.

---

*Built with [NextWork](https://nextwork.ai) - [View this project](https://nextwork.ai/projects/fbf2e057-439b-4ad8-bb1d-fd11ab79900e)*
