<img src="https://cdn.prod.website-files.com/677c400686e724409a5a7409/6790ad949cf622dc8dcd9fe4_nextwork-logo-leather.svg" alt="NextWork" width="300" />

# Prove an IAM Permissions Boundary

**Project Link:** [View Project](https://nextwork.ai/projects/e2e44316-c623-4228-b9e7-408e55294a50)

**Author:** Roy Piring: Cloud Platform Engineer | Build Master  
**Email:** rpiringhawaii@gmail.com

---

![Image](https://nextwork.ai/refreshed_maroon_timid_jujube/uploads/e2e44316-c623-4228-b9e7-408e55294a50_svnqhpby)

## Proving the Permissions Boundary Held the Security Ceiling

### Applying and validating the approved boundary

I attached the BoundaryProofCeiling permissions boundary to the test role and captured the resulting role configuration. I then ran Checks 3 through 6 to separate four authorization outcomes: explicit denial, implicit denial, boundary refusal, and permitted access. This proved how the role’s local policy and approved ceiling interacted instead of treating attachment alone as evidence that the control worked.

I compared eight frozen predictions with the simulator results and achieved an exact 8/8 match. The final evidence included the policy inputs, simulator responses, adjudication results, and a self-contained readout.html file. I committed those assets to the local Git repository only after the results were scored. The implementation demonstrated that the boundary constrained the tested widening while preserving the approved read operation. All AWS account numbers were masked in the written readout.

### Verifying the role's boundary configuration

The configuration proof came from the role’s PermissionsBoundary block after attachment. The response preserved in docs/build-record.md showed PermissionsBoundaryType as Policy and the ARN as arn:aws:iam::<ACCOUNT_ID>:policy/BoundaryProofCeiling. I replaced the 12-digit AWS account number with <ACCOUNT_ID> so the public documentation retained the resource structure without exposing the account identifier.

This block proved that IAM associated the test role with the intended customer-managed boundary. It was not evidence that the boundary had been attached as an identity permissions policy, because permissions boundaries and attached policies serve different functions. The local widened.json file also remained separate and was never presented as a deployed managed policy. I combined the configuration response with simulator results because the ARN proved linkage, while the decisions proved the effect of that linkage on authorization.

### Separating explicit deny, implicit deny, boundary refusal, and allowed access

Check 3 returned explicitDeny for five privileged role mutations because those actions matched DenyPrivilegedRoleMutation. The explicit refusal overrode any competing allow. Check 4 returned implicitDeny for iam:ListUsers because widened.json contained no matching allow. No statement matched, and the boundary could not grant authority missing from the identity policy.

Check 5 isolated the permissions ceiling. The local widening allowed iam:CreatePolicyVersion, but the boundary blocked it and reported AllowedByPermissionsBoundary as false. Check 6 provided the allowed control: iam:GetRole was permitted because both the local policy and the boundary allowed that read. These results showed why a generic denied label was insufficient. Each refusal had a different cause, and the allowed control proved the simulator was not denying every tested action.

## Demonstrating the Unbounded Widening Risk

### Building the controlled IAM policy and role

I created the BoundaryProofCeiling customer-managed policy and BoundaryProofRole through the AWS CLI, then recorded their generated ARNs in docs/build-record.md. Any 12-digit account number embedded in those ARNs was replaced with <ACCOUNT_ID> in the shareable documentation. The unmasked values remained only in the local command evidence required to operate on the temporary resources.

Before attaching the boundary, I ran aws iam simulate-principal-policy against the role and the widening defined in widened.json. The simulator returned allowed for iam:CreatePolicyVersion, and I saved the response as results/02-baseline.json. I then checked the captured result, updated docs/TASKS.md, and committed the baseline assets. This sequence established the uncontrolled risk before the ceiling existed and prevented the later refusal from being attributed to a missing identity-policy allow.

### What the allowed pre-boundary result establishes

The pre-boundary result proved that the local widening could grant iam:CreatePolicyVersion on its own. The test role did not have a permissions boundary at that stage, so IAM evaluated the widened identity permissions without the approved ceiling and returned allowed. This created the control condition required for the later comparison.

The result did not mean that a new policy version was created. simulate-principal-policy calculated the authorization decision without executing the requested IAM mutation. That distinction kept the test controlled while still proving the widening represented a meaningful authorization risk. After the boundary was applied, the same action reported AllowedByPermissionsBoundary as false. Because the identity allow had already been demonstrated, the changed decision could be attributed to the boundary rather than an absent permission or an unrelated simulator failure.

![Image](https://nextwork.ai/refreshed_maroon_timid_jujube/uploads/e2e44316-c623-4228-b9e7-408e55294a50_of76isn1)

## Sealing the Evidence with Verified Teardown

### Removing temporary IAM resources in dependency order

I removed the permissions boundary from the test role before deleting the temporary IAM resources. I inspected each dependency so an attachment or boundary association would not leave the environment in a partial state. The teardown sequence removed the role configuration first, deleted the role, and then deleted the BoundaryProofCeiling customer-managed policy.

After deletion, I ran fresh GetRole and GetPolicy requests against the exact resource identifiers used during the build. I preserved the resulting NoSuchEntity responses as absence evidence rather than relying only on successful delete commands. I then wrote the after-action review, completed the remaining entries in docs/TASKS.md, and sealed the repository with the annotated v1.0.0 tag. The teardown proved removal of the two named temporary resources, not the absence of unrelated IAM resources elsewhere in the AWS account.

### Why fresh NoSuchEntity reads prove resource absence

The absence evidence came from new GetRole and GetPolicy calls issued after teardown. IAM returned NoSuchEntity for BoundaryProofRole and for arn:aws:iam::<ACCOUNT_ID>:policy/BoundaryProofCeiling. I masked the account number in the published ARN while retaining the policy path and name required to understand which resource was checked.

These responses were stronger than handwritten cleanup notes because they came directly from AWS after the delete operations. An existing role or policy would have returned metadata unless another authorization or request error intervened. The recorded error therefore demonstrated that IAM could no longer resolve either named resource at verification time. The claim remained narrow: it proved that the temporary role and boundary policy used by this build were gone. It did not establish that the entire account had returned to an earlier configuration baseline.

![Image](https://nextwork.ai/refreshed_maroon_timid_jujube/uploads/e2e44316-c623-4228-b9e7-408e55294a50_tfk4fz96)

![Image](https://nextwork.ai/refreshed_maroon_timid_jujube/uploads/e2e44316-c623-4228-b9e7-408e55294a50_71jo0lip)

## Freezing an Auditable Security Decision

### Capturing requirements, policy inputs, and the evidence plan

I wrote five design documents under docs/ before deploying active AWS resources. They captured the requirements, decision records, topology, phase plan, and source map. I also created trust.json, boundary.json, and widened.json as separate policy inputs so the trust relationship, approved ceiling, and proposed widening could be reviewed independently.

I recorded eight expected authorization outcomes in predictions.csv before running the simulator. Those rows defined which actions should be allowed, explicitly denied, implicitly denied, or blocked by the boundary. I staged and committed the design files before resource creation so the repository showed that the evidence plan preceded the results. Git history supported this sequence but did not make it immutable, since repository history can be rewritten. The frozen files still provided a clear baseline for comparing predictions with the eight observed decisions.

### Preventing predictions from changing after results are known

The eight prediction rows formed the locked hypothesis for the boundary test. Each row stated the IAM decision expected after the boundary was applied. I wrote and committed them before running any policy simulation, which prevented an unexpected result from being converted into a match by editing the prediction afterward.

Validation compared each frozen row with its corresponding simulator decision and reported the result as matches out of eight. The final score was 8/8. That number carried meaning because the expected outcomes existed before the evidence, not because eight successful-looking values appeared in a report. The method also made disagreement visible: one unexpected decision would have reduced the score instead of being explained away. Git ordering supported the record, although protected branches, signed commits, or an independent timestamp would provide stronger assurance against later history rewriting.

![Image](https://nextwork.ai/refreshed_maroon_timid_jujube/uploads/e2e44316-c623-4228-b9e7-408e55294a50_dbm6659l)

## Establishing a Trusted Local Evidence Workspace

### Verifying a non-root administrative session and local repository

I verified the development tools, including the IDE, Git, and AWS CLI v2, before beginning the policy test. I captured the active AWS administrative caller identity as Check 1 in results/01-caller.json. In the shareable readout, I masked the returned AWS account number and any account-bearing ARN so the evidence preserved identity type and structure without publishing the account identifier.

I created the workspace folders, saved the master handoff ledger in docs/TASKS.md, and defined the six graded checks in results/check-manifest.md. I then initialized a local-only Git repository, configured the local author name and email, staged the initial setup files, and recorded the first commit. These actions established traceability for the test. They did not treat local administrator access as proof of AWS authorization; the caller response and IAM simulator outputs remained the authoritative evidence for cloud identity and policy decisions.

![Image](https://nextwork.ai/refreshed_maroon_timid_jujube/uploads/e2e44316-c623-4228-b9e7-408e55294a50_wnmbnqoc)

## Framing the Security Lead's Question

### Can the local widening grant CreatePolicyVersion after the boundary is applied?

I framed the test around one direct question: could the local widened.json policy grant iam:CreatePolicyVersion after the approved permissions boundary was attached? I first prepared the evidence workspace and froze the expected interactions between the identity policy and the ceiling. I then deployed the temporary role without a boundary and proved that the widening returned allowed.

After applying BoundaryProofCeiling, I repeated the authorization analysis and observed AllowedByPermissionsBoundary as false. The result showed that the role’s local allow could not exceed the approved ceiling. I compiled the decisions into readout.html, committed the adjudication evidence, tagged the repository, and removed the temporary resources. This conclusion applied to the tested policies, actions, role, and simulator inputs. It did not claim that every policy attached elsewhere in the account had been evaluated or that simulation replaced continuous monitoring of live IAM changes.

## Keeping Optional Analysis Outside the Graded Proof

### Preserving the separation between optional checks and sealed evidence

The graded proof remained limited to the IAM policy simulator, the eight frozen decisions, AllowedByPermissionsBoundary being false, and the six checks named in results/check-manifest.md. IAM Access Analyzer outputs were stored in optional-*.json files and remained outside the acceptance criteria, scoring logic, final readout, and tagged teardown evidence.

The optional check-no-new-access comparison returned FAIL, but that result did not change the 8/8 simulator score. It compared the two local policy documents and answered a different question from the boundary evaluation. Treating it as part of the graded path would have changed the test after evidence already existed. I therefore preserved it as additional analysis without allowing it to redefine the decision. This separation showed that an optional warning could remain visible while the original proof retained its declared scope, inputs, scoring method, and outcome.

![Image](https://nextwork.ai/refreshed_maroon_timid_jujube/uploads/e2e44316-c623-4228-b9e7-408e55294a50_wzk9l0kc)

## Reflecting on IAM Evidence Engineering

### Tools and authorization concepts applied

I used the AWS CLI, IAM roles, customer-managed policies, permissions boundaries, and simulate-principal-policy to build the evidence. The main authorization lesson was that an identity-policy allow was not sufficient when the permissions boundary omitted or denied the action. Check 5 demonstrated this through AllowedByPermissionsBoundary being false for iam:CreatePolicyVersion.

I also practiced separating policy roles. widened.json remained a local simulation input, while BoundaryProofCeiling was the boundary attached to the test role. I froze eight predictions before creating the resources, scored the returned decisions without changing the hypothesis, and verified teardown through fresh NoSuchEntity responses. The resulting evidence showed configuration, behavior, and cleanup as separate claims. Masking account numbers allowed the documentation to retain technical value without exposing the AWS account identifier.

### Completion time and implementation challenges

I completed the build in approximately 90 minutes. That time covered workspace preparation, AWS resource creation, boundary attachment, policy simulation, result adjudication, report generation, and teardown. The most difficult part was correcting AWS CLI syntax on Windows PowerShell, where parameter coercion and swapped resource ARNs caused commands to fail before the intended IAM evaluation could run.

I resolved those failures by checking the expected parameter types, confirming which ARN belonged to the role or managed policy, and rerunning the affected commands with corrected values. I did not score syntax failures as security evidence because they never reached the policy decision being tested. Troubleshooting them strengthened my understanding of how command construction, resource identity, and simulator context affected the result.

### Next steps in security and cloud engineering

I completed this build to gain practical experience enforcing and proving an IAM permissions ceiling through the AWS CLI. The unbounded control demonstrated that the local policy could allow iam:CreatePolicyVersion, while the bounded result showed that the same widening could not exceed BoundaryProofCeiling. The eight frozen predictions, six-check manifest, offline readout, and teardown evidence made the conclusion reviewable.

My next goal is to move these controls into a Security as Code workflow. I want policy changes tested automatically before deployment, with approved boundaries, frozen expected decisions, masked evidence, and machine-readable failures stored in CI/CD. That extension should reject unauthorized widening before it reaches a live role and preserve the same distinction between required proof and optional analysis. It should also keep account numbers and other sensitive identifiers out of public artifacts while retaining enough structure for reviewers to reproduce the

---

*Built with [NextWork](https://nextwork.ai) - [View this project](https://nextwork.ai/projects/e2e44316-c623-4228-b9e7-408e55294a50)*
