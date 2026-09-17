# Branch rules

`main-branch-protection.json` is the rule that protects the `main` branch of this repository. It is kept here so that everyone can read the rule, and so that changes to it are proposed and reviewed like any other change.

What the rule does:

- Every change to `main` comes through a pull request.
- A pull request needs one approving review before it can merge. GitHub does not let you approve your own pull request, so the approval comes from someone else.
- New commits pushed to a pull request clear any earlier approvals, so the approver sees the final version.
- All review comments must be resolved before merging.
- Nobody can delete `main` or force-push to it.
- Repository administrators can bypass these rules.

How to apply the file (repository administrator only):

1. Open the repository's Settings, then Rules, then Rulesets.
2. Choose New ruleset, then Import a ruleset.
3. Upload `main-branch-protection.json`.
4. Confirm that Enforcement status is Active.

If the rule changes, edit it in the GitHub settings, export it from the same menu, and open a pull request that replaces this file with the exported version, so the file and the live rule stay the same.
