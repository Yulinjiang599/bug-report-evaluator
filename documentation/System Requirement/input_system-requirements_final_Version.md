# Input Evaluation Subsystem Requirements

**Milestone:** Gate Review 2 revision  
**Status:** Review draft. Not ready to baseline until the open items in Section 9 are resolved.  
**Owners:** Julian Jiang (`Yulinjiang599`); Lurui Zheng (`lz668-zlr`)  
**Governing system requirements:** [System Requirements (R1-R9)](https://docs.google.com/document/d/16a4n3GOQUCMJNqrWSI4KCzBsWQk-W8XhA2J3N4qtpGE/edit?tab=t.0). Draft, no version identifier; content reviewed on 2026-10-29. The system requirements are being revised as part of the Gate 1 rework and are not yet baselined (OI-1).

## 1. Purpose and Boundary

Integration receives bug reports from GitHub and sends the original and structured report to Input Evaluation.

Input Evaluation is responsible for:

1. evaluating the report against the published quality criteria;
2. identifying checkable claims;
3. preserving the source context needed to inspect and investigate its outputs; and
4. returning findings and investigation requests to Integration.

Integration routes investigation requests to Claim Investigation and combines subsystem findings for the maintainer-facing output. Claim Investigation checks identified claims against public project artifacts.

This document flows Input Evaluation requirements down from the subsystem allocations in the current system requirements. It does not create new customer needs. The customer justification for the system-level requirements belongs in the system ConOps and system-requirements record (OI-2).

## 2. Requirement and Design Rule

A **requirement** states an externally observable result that Input Evaluation must produce, regardless of implementation.

A **design decision** states how the team currently plans to produce that result.

Example:

- Requirement: each identified claim has a unique identifier and is traceable to its source report and source passage.
- Design: a `claim_id` field and JSON records preserve that association.

Changing the field name or data format does not change the requirement as long as identifier uniqueness and traceability are preserved.

Verification methods follow the four standard methods: test, analysis, inspection, and demonstration.

## 3. Key Terms

Terms marked "system" are taken from the system-level definitions in [system-requirements.md](system-requirements.md). The others are defined here for this subsystem.

- **Checkable claim (system):** A statement in a report that can be confirmed or refuted using the project's public artifacts (code, version history, documentation, prior issues).
- **Finding (system):** A single statement the system makes about a report.
- **Historical evaluation (system):** A measurement of accuracy on bug reports whose outcomes are already known, drawn from at least three open source projects that differ in size and application domain. In this document, the historical evaluation set means the reports and human reference labels used for that measurement.
- **Processable report:** A report that satisfies the required conditions of the approved Integration-to-Input Evaluation interface contract.
- **Contract-invalid input:** An input that violates one or more required conditions of that interface contract. Contract-invalid inputs are handled by IE8.
- **Criterion finding:** Input Evaluation's result for one published quality criterion. A criterion finding contains either a conclusion or an explicit unevaluated outcome with a reason.
- **Unevaluated criterion:** A criterion for which Input Evaluation cannot reach a conclusion from the available processable input. This is different from concluding that required information is missing, which is a conclusion. The mapping of the published rating scale (P, M, N/A, U) to concluded and unevaluated outcomes is an open item (OI-10).
- **Evidence basis:** The inspectable evidence items or source references cited by a criterion finding. For a conclusion that information is present, it is the report content that supports the conclusion. For a conclusion that information is missing, it is an explanation of what required information was sought and a reference to where it was expected. For an unevaluated outcome, it is a reference to the content that could not be evaluated.
- **Identified claim:** A checkable claim recognized by Input Evaluation for downstream investigation.
- **Investigation request:** The information Input Evaluation provides for downstream investigation: one or more identified claims together with the report context required by the approved downstream interface.

## 4. System-to-Subsystem Flow-Down

Each row below cites a subsystem allocation stated in the governing system requirements.

| System-level source | Why Input Evaluation carries this behavior | Input Evaluation requirement |
|---|---|---|
| R1, R1.1, R1.2: evaluate every report; one finding per criterion; identify criterion ID and criteria version | The R1 allocation assigns criterion evaluation to Input Evaluation. | IE1 (processable reports), IE8 (all other inputs) |
| R3, R3.1: every finding cites evidence; every evidence item matches its source | The R3 allocation requires Input Evaluation to cite evidence for each criterion finding. R3.1 applies to every evidence item the system outputs. | IE2 |
| R4, R4.1-R4.3: state what could not be checked, why, and when nothing remains unchecked | The R4 allocation assigns unevaluated-criterion reporting to Input Evaluation. | IE3 |
| R7.1: identify at least 80% of labeled checkable claims | The R7 allocation assigns the 80% claim-identification target to Input Evaluation. | IE4 |
| R7: assign a unique claim identifier and preserve claim text, source, project, context, and named version | The R7 allocation assigns this to Input Evaluation. Claim Investigation cannot interpret or return a result for a claim that has lost its source context. | IE5 |
| R2, R2.1: deliver every finding for the report | The R2 allocation requires Input Evaluation to provide all criterion findings to Integration. | IE6 |
| R7: provide each investigation request and its context to Integration | The R7 allocation requires Input Evaluation to provide investigation requests to Integration for transfer to Claim Investigation. | IE7 |
| R8: return an explicit error or unevaluated result for unprocessable input | The R8 allocation assigns input validation to Input Evaluation. | IE8 |
| R8: consistent findings across style variation | The R8 allocation references a threshold in R9.3, which defines provenance instead. | Deferred; see Section 8.1 |
| R6: no record associating one reporter with more than one submission | The R6 allocation assigns this to Input Evaluation for its own records. | IE9 |
| R6: same findings when reporter identity is replaced with placeholders | The R6 allocation references a threshold in R6.1, which does not exist. | Deferred; see Section 8.2 |
| R5, R5.1-R5.3: system accuracy, coverage, and negative-finding correctness | The R5 allocation requires Input Evaluation findings to be comparable with the human-labeled historical set. | IE10 |
| R9.3: record versions used to produce each output | The R9 allocation requires Input Evaluation to record the criteria version and every external model version. | IE11 |

Input Evaluation will update this flow-down if the system requirements change before they are baselined.

## 5. Input Evaluation Requirements

### IE1 - Complete Criterion Evaluation

**Parent:** R1, R1.1, R1.2  
**Requirement:** For each processable report, Input Evaluation shall produce exactly one criterion finding for every published quality criterion. Each finding shall identify the criterion ID and the version of the quality criteria used.  
**Method:** Test  
**Verification:** Run the approved criterion test set (E11) and compare each output with the versioned criterion list (E4).  
**Pass:** Every processable test report has exactly one finding per published criterion, and every finding contains the correct criterion ID and criteria version.

R1 applies to every incoming report. A report that is not processable receives an explicit non-normal outcome under IE8. Together, IE1 and IE8 cover every report Input Evaluation receives.

### IE2 - Inspectable Evidence

**Parent:** R3, R3.1  
**Requirement:** Each criterion finding shall cite at least one inspectable evidence item or source reference and preserve its connection to the originating report content. Every quotation shall match the report text exactly, and every source reference shall resolve to the cited content.  
**Method:** Test; Inspection  
**Verification:**

1. Test: for every finding in the criterion test set, check that at least one evidence item or source reference is present, that each quotation matches the originating report text exactly, and that each reference resolves to the cited content.
2. Inspection: inspect the approved evidence-verification sample defined in the verification plan (E11) and determine whether the cited evidence supports the stated finding, including findings based on missing information.

**Pass:** Every tested finding cites evidence connected to the correct report; every quotation matches exactly and every reference resolves; and every inspected finding's evidence supports the stated finding.

### IE3 - Unevaluated Criteria

**Parent:** R4, R4.1, R4.2, R4.3  
**Requirement:** For each processable report, Input Evaluation shall identify every criterion it could not evaluate and state the reason. When it reaches a conclusion for every criterion, it shall explicitly state that no criterion remains unevaluated.  
**Method:** Test  
**Verification:** Process three groups of test reports: reports with intentionally non-evaluable criteria; reports in which required information is simply missing; and control reports for which every criterion can be evaluated.  
**Pass:** Every non-evaluable criterion is reported as unevaluated with a reason; no unevaluated criterion is presented as a conclusion; missing information is reported as a conclusion, not as unevaluated; and every control report states that no criterion remains unevaluated.

### IE4 - Checkable-Claim Recall

**Parent:** R7.1 and its Input Evaluation allocation  
**Requirement:** Input Evaluation shall identify at least 80% of the checkable claims labeled in the historical evaluation.  
**Method:** Test; Analysis  
**Verification:** Run Input Evaluation on the approved historical claim set (E10). Compare identified claims with human-labeled reference claims using the approved claim-matching rule (E11). Calculate recall as matched reference claims divided by total reference claims.  
**Pass:** Claim recall is at least 80%.

The 80% value comes directly from R7.1. Input Evaluation did not select it, and any change to it requires a system-level requirement change. Claim precision is not specified by R7.1; see Section 8.3.

### IE5 - Claim Identification and Traceability

**Parent:** R7 Input Evaluation allocation  
**Supports:** R7.3 (claim findings quote the report passage) and R7.5 (claims are checked against a named project version). Claim Investigation performs both, using the context Input Evaluation preserves.  
**Requirement:** Input Evaluation shall assign each identified claim a unique identifier and preserve its exact claim text, source report reference, project identifier, relevant report context, and any project version named in the report.  
**Method:** Test; Inspection  
**Verification:** Process reports containing multiple claims, including reports that name a project version. Using only the information Input Evaluation exposes, distinguish each claim and locate its source passage, report, project, context, and named version. Verify identifier uniqueness using the scope defined by the approved interface contract (OI-8).  
**Pass:** Every identified claim in the test set has an identifier that is unique within the scope defined by the approved interface contract, an exact claim text that matches the report, and the correct report, project, context, and named version when one is given.

### IE6 - Criterion-Finding Handoff

**Parent:** R2, R2.1, and the R2 Input Evaluation allocation  
**Requirement:** Input Evaluation shall provide every criterion finding, its evidence basis, and any unevaluated reason to Integration in the agreed interface format.  
**Method:** Test (interface)  
**Verification:** Compare the findings Input Evaluation generates with the records available at the Input Evaluation-to-Integration boundary.  
**Pass:** Every generated finding is available at the boundary with its evidence basis and any unevaluated reason.

### IE7 - Investigation-Request Handoff

**Parent:** R7 Input Evaluation allocation  
**Requirement:** For every identified claim, Input Evaluation shall provide that claim and the report context required by the approved downstream interface to Integration for transfer to Claim Investigation.  
**Method:** Test (interface)  
**Verification:** Process reports containing multiple claims and validate the information made available at the downstream boundary against the approved downstream interface contract (OI-7).  
**Pass:** Every identified claim is represented exactly once in the information made available at the downstream boundary, and all required context satisfies the approved interface contract.

### IE8 - Unprocessable Input

**Parent:** R8 Input Evaluation allocation  
**Requirement:** Input Evaluation shall validate each received report against the approved input contract. For each contract-invalid input, it shall return an explicit error or unevaluated result stating why the input could not be processed, and shall not represent the input as a normally completed evaluation.  
**Method:** Test (interface)  
**Verification:** Submit test inputs that each violate one required condition of the Integration-to-Input Evaluation interface contract (E7).  
**Pass:** Every contract-invalid test input produces an explicit error or unevaluated result with a reason, and none appears as a normally completed evaluation.

### IE9 - No Cross-Report Reporter Record

**Parent:** R6 Input Evaluation allocation  
**Requirement:** Input Evaluation shall not create or retain records that associate a reporter with more than one submission.  
**Method:** Inspection; Test  
**Verification:** Submit multiple reports, including several from the same reporter. Inspect Input Evaluation outputs and every record stored by Input Evaluation.  
**Pass:** No inspected Input Evaluation output or stored record associates one reporter with more than one submission.

### IE10 - Historical-Evaluation Comparability

**Parent:** R5 Input Evaluation allocation  
**Requirement:** Each criterion finding produced for the historical evaluation shall be matchable to the corresponding human-labeled reference unit required by the approved system evaluation procedure.  
**Method:** Test; Analysis  
**Verification:** Run Input Evaluation on the historical evaluation set (E10) and apply the approved matching procedure.  
**Pass:** Every criterion finding for a processable historical report is matched to the correct reference unit.

This requirement supports the system-level accuracy, coverage, and negative-finding tests in R5.1-R5.3. It does not assign those system targets to Input Evaluation alone.

### IE11 - Result Provenance

**Parent:** R9.3 Input Evaluation allocation  
**Requirement:** Each completed Input Evaluation output shall record the version of the quality criteria and every external model used to produce its findings.  
**Method:** Inspection; Test  
**Verification:** Inspect completed test outputs and check whether the criteria version and every external model version used can be identified.  
**Pass:** Every completed test output records the criteria version and all external model versions used.

## 6. Design and Interface Decisions

These are the current ways of satisfying the requirements. They are not additional subsystem requirements and may change without changing the requirements, provided the replacement still satisfies them.

| Current decision | Requirement supported |
|---|---|
| Represent the required unique claim identifier with a `claim_id` field. The field name and format are design choices. | IE5, IE7 |
| Use a structured interface record containing claim text, source reference, project identifier, context, and named version. | IE5, IE7 |
| Represent a report with no identified claims explicitly in the handoff, so that "no claims identified" cannot be confused with a missing output. | IE7; supports R4.3 |
| Use an agreed schema for Input Evaluation inputs and outputs, including concrete representations for unevaluated outcomes and errors. | IE3, IE6-IE8 |
| Choose the transport mechanism used to hand records to Integration (for example, a function call, an API, or files). | IE6, IE7 |
| Choose the model, prompt, algorithm, language, and storage mechanism during implementation. | Implementation choice |

## 7. Evidence Record

Each row links the evidence behind a requirement or decision. Rows marked TBD have no evidence yet and are tracked as open items.

| ID | Evidence | Supports | Link | Status |
|---|---|---|---|---|
| E1 | System Requirements R1-R9 with subsystem allocations | All flow-down in Section 4 | [Google Doc](https://docs.google.com/document/d/16a4n3GOQUCMJNqrWSI4KCzBsWQk-W8XhA2J3N4qtpGE/edit?tab=t.0) | Draft; no version identifier; reviewed 2026-10-29 (OI-1) |
| E2 | System-level definitions | Section 3 | [system-requirements.md](system-requirements.md) | Exists |
| E3 | ConOps and architecture records establishing subsystem responsibilities | Why each function is allocated to Input Evaluation | TBD | Not yet located (OI-2) |
| E4 | Quality criteria C1-C7 and rating scale | IE1-IE3 | [bug-report-quality-criteria.md](../bug-report-quality-criteria.md) | Exists; no version identifier (OI-9) |
| E5 | Manual calibration record: two independent evaluators rated 10 reports from 5 projects; 6 disagreements recorded and resolved | Criteria definitions in E4 | [Calibration Record](../bug-report-quality-criteria.md#calibration-record) and [Final Criteria Decisions](../bug-report-quality-criteria.md#final-criteria-decisions) | Exists |
| E6 | Published research supporting each criterion | Criteria definitions in E4 | TBD | E4 states that criteria are revised using relevant research but does not yet cite sources (OI-9) |
| E7 | Integration-to-Input Evaluation interface agreement | IE6, IE8, definition of processable report | [evaluation-integration-interface agreement.md](../Interface%20Agreemtn/evaluation-integration-interface%20agreement.md) | Draft; approval required (OI-6) |
| E8 | Input Evaluation-to-Integration investigation-request interface | IE7 | TBD | Not yet drafted (OI-7) |
| E9 | Input Evaluation working agreement, including how decisions are made and recorded | Decision process | [input-evaluation.md](../subsystem-working-agreements/input-evaluation.md) | Exists |
| E10 | Human-labeled historical reports and claim labels | IE4, IE10 | TBD | Owner to be confirmed (OI-11) |
| E11 | Verification plan: criterion test set, evidence-verification sample and sampling procedure, invalid-input cases, claim-matching rule | IE1-IE8 | TBD | Not yet drafted (OI-11) |
| E12 | Decision records for significant requirement or design changes | Decision process | TBD | None yet; Section 10 lists the changes that need records |

## 8. Unresolved System-Level Issues

The following statements appear in the current system requirements but cannot yet be turned into verifiable Input Evaluation requirements. Input Evaluation will not invent replacement values.

### 8.1 Style-Variation Threshold

The R8 allocation requires consistent findings for semantically equivalent reports whose formatting or writing style differs, "subject to the system-level threshold in R9.3." R9.3 defines provenance, not a threshold. The system-level requirements owner needs to correct the reference and define the comparison rule and threshold (OI-3).

### 8.2 Identity-Independence Threshold

The R6 allocation requires the same findings when reporter-identifying information is replaced with placeholders, "subject to the system-level threshold in R6.1." R6.1 does not exist. R6 contains an unnumbered statement ("The system shall keep no record that associates a reporter with more than one submission"), but it defines no threshold. The system-level requirements owner needs to add R6.1 or correct the reference (OI-4).

### 8.3 Claim Precision

R7.1 specifies claim recall but no precision or false-positive limit, so Input Evaluation does not set one. Over-identification still has a system-level cost: R7.2 requires one finding for every identified claim, so over-identification increases downstream investigation work. It may also affect system evaluation metrics such as R5.2 coverage, depending on how the approved system evaluation procedure defines them. The system-level team, Input Evaluation, and Claim Investigation should decide whether a precision requirement is needed (OI-5).

### 8.4 System Requirements Affected by Input Evaluation but Not Allocated

The following system requirements have no Input Evaluation allocation, but Input Evaluation's behavior may affect whether the system meets them. Input Evaluation will not set its own budgets for them. The system-level requirements owner should decide whether an allocation is needed (OI-12).

| System requirement | How Input Evaluation may affect it |
|---|---|
| R2.2: 95% of outputs within 15 minutes | Input Evaluation processing time uses part of this budget. |
| R5.2, R5.3: coverage of at least 80%; at least 90% (TBR-5) of negative findings correct | Unevaluated criteria and "missing" conclusions may affect the R5.2 coverage and R5.3 negative-finding metrics. The approved system evaluation procedure must define how these outcomes are counted. |
| R8.1: deliver an output when a source the system depends on is unavailable | If the selected Input Evaluation implementation depends on an external model or another source that can become unavailable, the system-level owner should determine whether Input Evaluation needs a specific fallback allocation under R8.1. |
| R9.2: two runs within 2 percentage points | If Input Evaluation uses an external model, run-to-run variation in its output may affect the reproducibility of criterion findings. |

## 9. Open Items Before Baseline

SSE refers to the system-level requirements owner; the name is to be confirmed. Target dates are TBD unless an owner has confirmed one. The README sets Gate 2 on 2026-12-07 and requires finished interface agreements at Gate 2, so OI-6 and OI-7 carry that date as a proposed target.

| ID | Open item / required action | Owner | Target | Status |
|---|---|---|---|---|
| OI-1 | Complete the Gate 1 rework, approve R1-R9 as the system baseline, and give it a version identifier. | SSE | TBD | Open |
| OI-2 | Locate and link the ConOps and architecture records that establish the Input Evaluation allocation (E3). | SSE, with Input Evaluation | TBD | Open |
| OI-3 | Correct the R8 style-variation reference to R9.3 and define the comparison rule and threshold. | SSE | TBD | Open |
| OI-4 | Add the missing R6.1 identity-independence threshold or correct the R6 reference. | SSE | TBD | Open |
| OI-5 | Decide whether claim precision or a false-positive limit is required in addition to R7.1. | SSE, Input Evaluation, Claim Investigation | TBD | Open |
| OI-6 | Review and approve the Integration-to-Input Evaluation interface agreement (E7), naming one owner from each subsystem. | Input Evaluation and Integration | Proposed: before Gate 2 (2026-12-07), pending owner confirmation | Draft exists |
| OI-7 | Define and approve the investigation-request interface. Claim Investigation validates each claim record against its own input contract and rejects invalid records, so it must review this interface. | Input Evaluation, Integration, Claim Investigation | Proposed: before Gate 2 (2026-12-07), pending owner confirmation | Open |
| OI-8 | Confirm that the unique claim identifier is an interface constraint and define its uniqueness scope (for example, within a report, a run, a project, or the whole system). Both the Input Evaluation and Claim Investigation allocations under R7 reference it. The field name and format remain design choices. | SSE, Claim Investigation | TBD | Open |
| OI-9 | Give the quality-criteria document (E4) a version identifier and cite the research sources behind each criterion (E6). | Input Evaluation | TBD | Open |
| OI-10 | Define how each rating-scale value (P, M, N/A, U) maps to a concluded or unevaluated outcome. U currently covers both contradictory information and inaccessible evidence, and the mapping affects R4 reporting and R5.2 coverage. | Input Evaluation | TBD | Open |
| OI-11 | Draft the verification plan (E11) and obtain the historical evaluation set (E10). | Input Evaluation for E11; owner of E10 to be confirmed | TBD | Open |
| OI-12 | Decide whether R2.2, R5.2, R5.3, R8.1, and R9.2 need an Input Evaluation allocation (Section 8.4). | SSE, with Input Evaluation | TBD | Open |

Until these items are resolved, affected values and references will remain open rather than being invented by Input Evaluation.

## 10. Change History

| Date | Change | Reference |
|---|---|---|
| 2026-10-05 | First draft of Input Evaluation subsystem requirements. | Commit `8057f42` |
| 2026-10-16 | Added verification methods and draft tests. | Commit `e69ca81` |
| 2026-10-18 | Moved the file into `documentation/System Requirement/`. | Commit `2514238` |
| 2026-10-27 | Revised requirements, separated requirements from design decisions, and listed derived requirements pending allocation. | Commit `7ce07ea` |
| 2026-10-29 | Re-derived every requirement from the subsystem allocations in the R1-R9 system requirements draft. Renumbered requirements IE1-IE11. Added key terms, R3.1 evidence matching, the explicit unique claim identifier, verification methods, and an evidence record. Moved the style and identity requirements to Section 8 until their system-level thresholds exist. Replaced unconfirmed due dates with TBD. | This pull request |
