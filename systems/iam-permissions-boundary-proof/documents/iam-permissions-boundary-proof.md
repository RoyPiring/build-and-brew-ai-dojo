<img src="https://cdn.prod.website-files.com/677c400686e724409a5a7409/6790ad949cf622dc8dcd9fe4_nextwork-logo-leather.svg" alt="NextWork" width="300" />

# Prove an IAM Permissions Boundary

**Project Link:** [View Project](https://nextwork.ai/projects/e2e44316-c623-4228-b9e7-408e55294a50)

**Author:** Roy Piring: Cloud Platform Engineer | Build Master  
**Email:** rpiringhawaii@gmail.com

---

![Image](https://nextwork.ai/refreshed_maroon_timid_jujube/uploads/e2e44316-c623-4228-b9e7-408e55294a50_svnqhpby)

## Proving the Permissions Boundary Held the Security Ceiling

### Applying and validating the approved boundary

I built this system to prove whether an AWS IAM permissions boundary could prevent a role-level policy from granting authority above an approved ceiling. I treated the boundary as a testable control rather than assuming that attaching it made the role safe. The implementation compared policy simulator decisions before and after attachment, then preserved each result as evidence.

I applied BoundaryProofCeiling to BoundaryProofRole and evaluated the actions frozen in my eight-decision prediction set. The tests covered permitted reads, attempted privilege expansion, explicit refusals, and actions omitted from the local policy. I required the observed simulator result for every action to match its prediction. This let me demonstrate not only that the boundary existed, but that it constrained effective permissions exactly where expected. The completed comparison scored 8/8 without changing the prediction file after results were known.

### Verifying the role's boundary configuration

I captured the complete get-role response in docs/build-record.md after running put-role-permissions-boundary. The returned JSON contained a PermissionsBoundary object whose PermissionsBoundaryArn was arn:aws:iam::<account-id>:policy/BoundaryProofCeiling. That ARN matched the customer-managed policy created from boundary.json, including the account number and policy name recorded in the build evidence.

This check established the configuration link between the role and the approved ceiling. A simulator result alone could show a refusal, but without the role response it would not prove which boundary was attached when the evaluation ran. I therefore preserved both the policy identity and the role configuration. The evidence did not infer attachment from a filename or command transcript. It used AWS returning the boundary ARN on a fresh role read, which tied the tested role to the exact policy used as the security ceiling.

### Separating explicit deny, implicit deny, boundary refusal, and allowed access

I separated four outcomes because a single denied label would have hidden why an action failed. Check 3 returned explicitDeny for five privileged mutations, proving that an explicit refusal overrode any matching allow. Check 4 returned implicitDeny for iam:ListUsers because widened.json supplied no applicable allow. The boundary could restrict authority, but it could not grant an action missing from the role policy.

Check 5 tested the central widening attempt. It refused iam:CreatePolicyVersion, with AllowedByPermissionsBoundary reported as false even though the widened local policy attempted to allow it. Check 6 returned allowed for iam:GetRole because both the role policy and boundary permitted that read. These distinct results showed intersection semantics: effective authority existed only where the identity policy and boundary both allowed it, while an explicit deny remained decisive.

## Demonstrating the Unbounded Widening Risk

### Building the controlled IAM policy and role

I created a temporary IAM role and policy set from frozen JSON inputs to establish the unbounded control condition. The role represented a workload identity whose local permissions could be widened, while widened.json contained the disputed iam:CreatePolicyVersion allowance. I created these resources before attaching the boundary so the simulator could show the risk that existed without the approved ceiling.

The control was deliberately narrow. I did not perform the privileged mutation against a live policy, and I did not create a production identity. Instead, I used simulate-principal-policy to calculate the effective authorization decision for the temporary role. I preserved the role name, policy names, ARNs, simulator input, and returned decision in the evidence record. That sequence provided a defensible before state: the same local widening that was later refused by the boundary was first shown as allowed while the role remained unbounded.

### What the allowed pre-boundary result establishes

The pre-boundary simulator result returned allowed for iam:CreatePolicyVersion against BoundaryProofCeiling. This established that the widening in the local policy was capable of granting the disputed action when no permissions boundary constrained the role. The later refusal therefore represented a measured change in effective authorization, not an action that had already been unavailable for some unrelated reason.

The result remained a simulation, not a live mutation. I did not create a new policy version, alter the ceiling, or exercise the permission against an AWS resource. The evidence proved that IAM policy evaluation would allow the action under the tested unbounded configuration. It did not prove that every downstream request would succeed, since service conditions and resource state could still matter. Within that boundary, the control gave the comparison a valid starting point and made the security shortfall falsifiable.

![Image](https://nextwork.ai/refreshed_maroon_timid_jujube/uploads/e2e44316-c623-4228-b9e7-408e55294a50_of76isn1)

## Sealing the Evidence with Verified Teardown

### Removing temporary IAM resources in dependency order

I removed the temporary IAM resources after completing the simulator checks so the evaluation would not leave an unnecessary role or policy in the account. I followed dependency order: I removed the permissions boundary from the role, detached the local policy, deleted the role, removed any policy dependencies, and then deleted BoundaryProofCeiling. This avoided treating dependency errors as evidence that cleanup had succeeded.

I recorded each teardown command and its response before sealing the repository. I then performed fresh reads for the exact role and policy identities created during the build. Cleanup was part of the acceptance criteria rather than an informal task performed after the proof. The final evidence tag therefore represented both the authorization findings and the restored environment. I did not claim that the entire AWS account matched an earlier baseline; I proved that the two named temporary resources used by this implementation were absent after deletion.

### Why fresh NoSuchEntity reads prove resource absence

I issued fresh get-role and get-policy requests after the deletion commands completed. AWS returned NoSuchEntity for BoundaryProofRole and for arn:aws:iam::<account-id>:policy/BoundaryProofCeiling. These were active read attempts against the exact names and ARN recorded during creation, not conclusions drawn from missing console rows or skipped commands.

The errors mattered because an existing IAM object would have returned metadata or a different authorization failure. NoSuchEntity showed that AWS could no longer resolve either tested identity at the time of verification. This evidence was scoped carefully: it proved absence of the named temporary role and managed policy, not the absence of similarly configured resources elsewhere in the account. By preserving the failed reads beside the successful deletion sequence, I made teardown independently reviewable and prevented a completed delete command from serving as the only cleanup claim.

![Image](https://nextwork.ai/refreshed_maroon_timid_jujube/uploads/e2e44316-c623-4228-b9e7-408e55294a50_tfk4fz96)

![Image](https://nextwork.ai/refreshed_maroon_timid_jujube/uploads/e2e44316-c623-4228-b9e7-408e55294a50_71jo0lip)

## Freezing an Auditable Security Decision

### Capturing requirements, policy inputs, and the evidence plan

I created the requirements, architecture notes, decision records, topology, phase plan, and source map before executing the IAM simulations. These documents fixed the security question, resource names, policy inputs, six graded checks, eight predicted decisions, teardown requirements, and evidence locations. The build could then be evaluated against declared criteria instead of a narrative assembled after the outcomes were visible.

I also preserved boundary.json, widened.json, and predictions.csv as distinct inputs. This separation made it possible to trace every result to the ceiling, the attempted local widening, and the expected simulator decision. The evidence plan identified which AWS responses had to be retained and which claims each response could support. That structure kept configuration, prediction, observation, and interpretation separate, allowing a reviewer to reconstruct the proof without relying on my final summary alone.

### Preventing predictions from changing after results are known

I froze all eight predictions before running simulate-principal-policy. Each row in predictions.csv identified an action and the result I expected from the interaction between boundary.json and widened.json. Once simulator output existed, I treated that file as immutable. This prevented me from converting unexpected decisions into apparent successes by rewriting the expected values afterward.

The method turned the exercise into a falsifiable test. Every returned decision could either match or contradict a statement already recorded on disk. I could therefore report an honest score of matches out of eight rather than describing the outcomes selectively. The final result was 8/8, but the value came from the ordering of the evidence as much as the number itself. A perfect score written after the run would prove little. A frozen prediction set, retained inputs, and row-level outputs showed that the boundary behaved as anticipated.

![Image](https://nextwork.ai/refreshed_maroon_timid_jujube/uploads/e2e44316-c623-4228-b9e7-408e55294a50_dbm6659l)

## Establishing a Trusted Local Evidence Workspace

### Verifying a non-root administrative session and local repository

I established a local evidence workspace before creating any AWS resources. I verified Git and the AWS CLI, confirmed the active identity and account, and checked that I was operating from a non-root local session. I initialized a dedicated repository so requirements, policy documents, simulator outputs, teardown records, and decision notes could be versioned together without mixing them with unrelated system files.

This preparation gave the later evidence a known execution context. The caller identity tied the AWS commands to account <account-id>, while the repository history showed when policy inputs and predictions were committed relative to the simulator results. I did not treat local administrator access as proof of AWS authorization; those were separate trust boundaries. The local checks established who ran the tooling and where records were stored, while AWS responses established what the cloud identity could do. Together, they formed a traceable starting point for the security

![Image](https://nextwork.ai/refreshed_maroon_timid_jujube/uploads/e2e44316-c623-4228-b9e7-408e55294a50_wnmbnqoc)

## Framing the Security Lead's Question

### Can the local widening grant CreatePolicyVersion after the boundary is applied?

I tested whether widened.json could grant iam:CreatePolicyVersion after the approved permissions boundary was attached to BoundaryProofRole. This was the central security question because creating a new managed-policy version could let a role expand policy content beyond the intended local permission set. The proof needed to show both that the widening worked without a ceiling and that the same action failed once the ceiling applied.

The measured answer was no. Before attachment, the simulator returned allowed, establishing that the identity policy could grant the action. After attachment, the result was refused and AllowedByPermissionsBoundary was false. I did not infer safety merely from the presence of a boundary ARN. I connected configuration evidence with before-and-after authorization decisions. The conclusion remained limited to the tested policies, role, actions, account, and simulator behavior captured by this build.

## Keeping Optional Analysis Outside the Graded Proof

### Preserving the separation between optional checks and sealed evidence

I kept optional IAM Access Analyzer work outside the graded proof so additional observations could not alter the predeclared score. Optional artifacts used the optional- prefix and were excluded from results/check-manifest.md, which continued to list only checks 1 through 6. The scored record remained 8/8 and traced solely to the frozen predictions and simulator outputs required by the evidence plan.

I also left the optional files uncommitted when I created the v1.0.0 tag. That ensured the tag continued to identify the sealed post-teardown proof rather than a later analytical branch. validate-policy incurred no charge, while the single paid comparison cost $0.002. I recorded that cost separately and did not fold it into the graded 0.00 USD path. This separation preserved two honest claims: the required proof used the declared no-cost route, and later optional analysis existed without being presented as part of that result.

![Image](https://nextwork.ai/refreshed_maroon_timid_jujube/uploads/e2e44316-c623-4228-b9e7-408e55294a50_wzk9l0kc)

## Reflecting on IAM Evidence Engineering

### Tools and authorization concepts applied

I used the AWS CLI to create temporary IAM resources, attach the permissions boundary, run policy simulations, inspect configuration, and verify teardown. I used Git to preserve the sequence between frozen inputs, observed outputs, and the tagged evidence state. I also used IAM Access Analyzer for optional policy checks that remained outside the graded proof.

The main concept I developed was that a permissions boundary limits maximum effective authority but does not grant permissions by itself. I distinguished explicit deny, implicit deny, boundary refusal, and mutual allowance rather than collapsing every non-allowed decision into one category. I also applied evidence-engineering controls: predictions were frozen before execution, every result traced to retained source data, and optional analysis remained separate. These practices made the conclusion reviewable without overstating what simulation could establish.

### Completion time and implementation challenges

I completed the build in approximately 60 minutes. The hardest part was maintaining a strict boundary between the six graded checks and the optional IAM Access Analyzer work. Once extra policy analysis existed, it would have been easy to let those results influence the score or appear inside the evidence represented by the v1.0.0 tag. I instead kept the optional files outside the manifest and left them uncommitted at the seal.

The implementation also required careful dependency handling during teardown. A permissions boundary, attached identity policy, role, and managed policy could not be removed safely in an arbitrary order. I recorded each operation and followed deletion with fresh reads against the exact resource identifiers. That work added operational evidence to the authorization proof. The result showed not only how the boundary behaved, but also that the temporary IAM objects were removed after testing.

### Next steps in security and cloud engineering

I completed this build to learn how to prove an IAM permissions boundary through controlled policy simulation, frozen predictions, configuration reads, and verified teardown. The result demonstrated that iam:CreatePolicyVersion was allowed by the unbounded local widening but refused after the approved ceiling was attached. It also showed why boundary presence, simulator output, and cleanup evidence needed to be preserved as separate facts.

My next step is to place the same checks into a CI/CD policy-validation workflow. I want proposed IAM changes to be evaluated against an approved boundary before deployment, with expected decisions stored as versioned test cases. That extension would need isolated test identities, protected policy fixtures, machine-readable results, and cleanup controls equivalent to this build. It should retain the same evidence boundary: simulations can validate authorization logic, but they do not replace live operational monitoring or prove the security of an en

---

*Built with [NextWork](https://nextwork.ai) - [View this project](https://nextwork.ai/projects/e2e44316-c623-4228-b9e7-408e55294a50)*
