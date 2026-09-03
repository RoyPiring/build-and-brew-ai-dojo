<img src="https://cdn.prod.website-files.com/677c400686e724409a5a7409/6790ad949cf622dc8dcd9fe4_nextwork-logo-leather.svg" alt="NextWork" width="300" />

# Build a Typed Crossplane v2 API

**Project Link:** [View Project](https://nextwork.ai/projects/291fe0ff-09fa-42b0-9be4-4d38914f2c14)

**Author:** Roy Piring: Cloud Platform Engineer | Build Master  
**Email:** rpiringhawaii@gmail.com

---

![Image](https://nextwork.ai/refreshed_maroon_timid_jujube/uploads/291fe0ff-09fa-42b0-9be4-4d38914f2c14_di13deaf)

## Reconciling a Healthy Application Environment

### Configuring continuous environment reconciliation

I defined a Crossplane Composition that translated an ApplicationEnvironment request into concrete Kubernetes resources. The Composition connected the typed platform API with the Deployment and Service needed to run the application.

This design let users declare the intended application state through one namespaced API instead of creating each Kubernetes object separately. The platform could then apply its schema, admission rules, Composition pipeline, and provider behavior through a controlled path.

The implementation was built for continuous reconciliation rather than a one-time resource creation. Crossplane remained responsible for comparing the requested state with the resources in the cluster. If a managed object disappeared, the control plane could detect the difference and recreate it without requiring another apply of the original ApplicationEnvironment.

### Evidence of continuous reconciliation

I deleted the queue-api Deployment and confirmed that it was gone by receiving NotFound. The ApplicationEnvironment and Service remained in the cluster, so the declared platform request still existed while one of its composed resources was missing.

Crossplane detected the difference and recreated the Deployment without another apply command. The restored Deployment returned to 1/1 readiness, and the rollout status completed successfully.

The order of evidence mattered. The Deployment first existed, then was deleted, then returned while the parent request remained unchanged. That sequence showed that reconciliation preserved desired state after disruption. A one-time generator could create the original resource, but it would not explain the automatic return after deletion. The recovery therefore demonstrated continuous control rather than a single successful creation.

## Enforcing a Typed API with Admission Controls

### Building the typed ApplicationEnvironment API

I built a Crossplane v2 CompositeResourceDefinition for a namespaced ApplicationEnvironment API. The schema defined the required request fields and their expected types before I added the field-level CEL admission rules.

The API contract covered application identity, container image, port, and replica settings. Defining these fields in the XRD gave the Kubernetes API server a known structure to inspect when a user submitted an ApplicationEnvironment object.

This base schema separated valid platform inputs from the resources created later by the Composition. Admission handled request shape and policy first. Only requests that passed those checks could be stored and reach reconciliation. This ordering kept invalid values from entering the control plane and made validation errors appear at the request boundary rather than after a Deployment or Service had already been created.

### Why invalid requests are rejected before reconciliation

CEL admission rules ran in the Kubernetes API server when each ApplicationEnvironment request was checked. A server-side dry run followed the same admission path but stopped before storing the object in the cluster.

Reconciliation began only after a valid request was persisted. Crossplane could then evaluate the Composition, call Function Patch and Transform, and use provider-kubernetes to manage the resulting resources.

The image value nginx:latest failed the spec.image admission rule. Because the request was rejected at that boundary, no ApplicationEnvironment was stored and no Deployment or Service work began. This sequence separated request validation from resource reconciliation. The control plane did not create an invalid object and repair it later. It refused the request before any composed resource entered the execution path.

![Image](https://nextwork.ai/refreshed_maroon_timid_jujube/uploads/291fe0ff-09fa-42b0-9be4-4d38914f2c14_zkiiesx6)

## Defining and Preserving the Platform Contract

### Creating the contract pack

I wrote the platform contract before implementing the full validation and reconciliation path. The pack defined the ApplicationEnvironment boundary, required behaviors, acceptance criteria, and evidence needed to judge the final API.

The contract described a namespaced Crossplane v2 pattern that accepted valid application requests, rejected invalid fields during admission, and reconciled approved requests into Kubernetes resources. It also separated current patterns from legacy controls used only for comparison.

Writing these boundaries first gave later implementation choices a fixed reference. The XRD, CEL rules, Composition, functions, provider configuration, dry-run tests, and deletion test could each be checked against declared behavior. This prevented the final result from defining success after the fact and kept AI-generated suggestions outside the protected platform files until they had been reviewed.

![Image](https://nextwork.ai/refreshed_maroon_timid_jujube/uploads/291fe0ff-09fa-42b0-9be4-4d38914f2c14_3t0vyl54)

### Separating the AI draft from the platform implementation

I saved the complete Prompt 1 response only in evidence/rules-draft.md. I copied it unchanged so the original AI proposal remained available for review and comparison.

I did not convert that response directly into an XRD, Composition, function, or other platform file. Review notes were written separately in docs/readout.md under Human corrections to Prompt 1. Those notes recorded the human filter applied to the draft without creating another altered copy of the original response.

The protected implementation remained separate until specific rule fragments had been reviewed and accepted. This boundary preserved provenance and prevented an unreviewed AI answer from becoming cluster policy. The evidence file showed what the model proposed, the readout showed what required correction, and the platform files contained only the rules and structures approved for the human-built implementation.

![Image](https://nextwork.ai/refreshed_maroon_timid_jujube/uploads/291fe0ff-09fa-42b0-9be4-4d38914f2c14_l5xnc9a8)

## Launching a Healthy Local Control Plane

### Setting up the local platform control plane

I prepared a local Kubernetes control plane and installed Crossplane 2.4.0 with provider-kubernetes and Function Patch and Transform. These components supplied the runtime needed to validate ApplicationEnvironment requests and reconcile them into native Kubernetes resources.

I configured the required ProviderConfigs so provider-kubernetes could manage objects inside the local cluster. The Composition pipeline then had a working path from the accepted composite request through the function and provider to the resulting Deployment and Service.

The setup established the environment needed for both admission and reconciliation tests. CEL rules could reject invalid objects through server-side dry runs, while valid objects could be stored and composed. The later deletion test depended on this healthy control plane because Crossplane needed to remain active long enough to observe drift and restore the missing Deployment.

![Image](https://nextwork.ai/refreshed_maroon_timid_jujube/uploads/291fe0ff-09fa-42b0-9be4-4d38914f2c14_4tfn1rnb)

## Testing Scope and Refusal Boundaries

### What legacy controls and current-pattern checks proved

The legacy control passed its server-side dry run with exit code 0. Kubernetes admission therefore still accepted a v1 LegacyCluster XRD. This showed that a stale pattern was not automatically invalid to the API server.

The separate scope script inspected the live XRD and printed only Namespaced. That result confirmed the current Crossplane v2 pattern selected for the ApplicationEnvironment API. The control file did not establish that conclusion because its purpose was to test compatibility with an older form.

Both checks were required because they answered different questions. Admission showed whether Kubernetes accepted the specimen, while the scope script and rubric judged whether the accepted specimen matched the current platform contract. A successful dry run could prove syntactic compatibility, but it could not prove that the selected API pattern was current or appropriate.

![Image](https://nextwork.ai/refreshed_maroon_timid_jujube/uploads/291fe0ff-09fa-42b0-9be4-4d38914f2c14_q81246wv)

## Building Foundations for a Kubernetes Platform

### Project goals and admission scope

I built a namespaced Crossplane v2 ApplicationEnvironment API that used CEL rules to reject invalid application requests during Kubernetes admission. The contract covered application values, container images, ports, and replicas before reconciliation began.

The API provided one typed request surface for creating the Kubernetes Deployment and Service defined by the Composition. Valid requests continued through Function Patch and Transform and provider-kubernetes, while invalid requests stopped at the API server.

The scope check confirmed that the live XRD used the Namespaced pattern. Server-side dry runs measured whether each request passed admission without storing it. Together, these controls separated schema and policy enforcement from resource creation. The result was a platform API that refused bad inputs early and continuously maintained the resources produced from accepted requests.

## Reflecting on Platform Engineering Skills

### Tools and concepts developed

I used Docker, kind, kubectl, Helm, Crossplane 2.4.0, provider-kubernetes, and Function Patch and Transform. These tools created the local cluster, installed the control plane, defined the typed API, and reconciled valid requests into Kubernetes resources.

The central concept was a namespaced application contract enforced with CEL. Admission rules checked fields before the object was stored, while the Composition pipeline handled accepted requests afterward. Server-side dry runs let me test refusal behavior without leaving invalid resources in the cluster.

I also learned to separate compatibility from current-pattern selection. A legacy v1 XRD could pass admission while still failing the design rubric. The deletion test added another lesson: a managed Deployment returning after removal proved continuous reconciliation, not merely successful generation during the first apply.

### Project timeline and challenges

This build took approximately 65 minutes. The hardest part was writing CEL rules that produced the intended field-level validation errors for invalid application values, images, ports, and replicas.

The rules had to run during admission and reject the request before Crossplane started the Composition pipeline. A weak or misplaced expression could allow invalid data into reconciliation or return an error that did not identify the relevant field.

I also had to measure the difference between stale and invalid patterns. The legacy v1 control passed admission, so exit code alone could not reject it. The live scope script and rubric were required to confirm the Namespaced Crossplane v2 selection. Keeping these tests separate prevented Kubernetes compatibility from being reported as proof that an older specimen matched the current platform contract.

### Learning goals and next steps

I completed this build to learn how a typed Crossplane v2 API could enforce field-level rules and continuously reconcile approved requests into Kubernetes services. The XRD defined the contract, CEL rejected invalid inputs, and the Composition created the Deployment and Service.

The deletion test confirmed that the control plane maintained desired state. After queue-api was removed and returned NotFound, Crossplane recreated it without another apply, restored 1/1 readiness, and completed the rollout.

My next step is to add automated CI/CD tests for the platform API. Those checks should cover valid requests, each CEL refusal rule, namespaced scope, Composition output, provider health, and recovery after managed-resource deletion. This would preserve the same contract as requirements change and make regressions visible before they reach the local or shared control plane.

---

*Built with [NextWork](https://nextwork.ai) - [View this project](https://nextwork.ai/projects/291fe0ff-09fa-42b0-9be4-4d38914f2c14)*
