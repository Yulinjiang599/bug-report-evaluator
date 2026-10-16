## Input Evaluation Subsystem

### Definitions

- An **input report** is a bug report received by the Input Evaluation subsystem for evaluation.
- A **report context** is the information from the original bug report needed to interpret a checkable claim, such as the report title, body, repository identifier, issue URL, and surrounding text.
- An **identified claim** is a checkable claim extracted or recognized by the Input Evaluation subsystem for further investigation.

### Requirements & Verification


| ID  | Draft Requirement                                                                                                                          | Verification Method | Draft Test                                                                                                                                                                                  |
| --- | ------------------------------------------------------------------------------------------------------------------------------------------ | ------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| IE1 | The Input Evaluation subsystem shall evaluate every input report against the project-defined quality criteria.                             | Test                | Provide reports representing different criterion conditions and verify that every applicable criterion receives an evaluation result.                                                       |
| IE2 | The Input Evaluation subsystem shall identify checkable claims contained in each input report.                                             | Test                | Provide reports containing known checkable and non-checkable statements and compare the identified claims with a human-labeled reference set.                                               |
| IE3 | The Input Evaluation subsystem shall associate each identified claim with the report context required for downstream investigation.        | Inspection / Test   | Verify that each identified claim is accompanied by the report context specified in the subsystem interface agreement.                                                                      |
| IE4 | The Input Evaluation subsystem shall provide each identified claim and its associated report context to the Claim Investigation subsystem. | Interface Test      | Send a representative report through Input Evaluation and verify that Claim Investigation receives all identified claims and required context in the agreed interface format.               |
| IE5 | The Input Evaluation subsystem shall identify each applicable quality criterion that it cannot evaluate.                                   | Test                | Provide reports with missing, ambiguous, or inaccessible information and verify that criteria that cannot be evaluated are explicitly identified rather than assigned unsupported findings. |
| IE6 | The Input Evaluation subsystem shall preserve source traceability for each identified claim.                                               | Inspection / Test   | Verify that each identified claim contains a source reference sufficient to locate the original statement in the bug report.                                                                |
| IE7 | The Input Evaluation subsystem shall not compile information about individual people across reports.                                       | Inspection / Test   | Process multiple reports, including reports from the same contributor, and verify that the subsystem does not create or retain cross-report contributor profiles.                           |


