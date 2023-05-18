# [Sponsorname] Audit

Unless otherwise discussed, this repo will be made public after audit completion, sponsor review, judging, and issue mitigation window.

**Contributors to this repo:** prior to report publication, please review the [Agreements & Disclosures](/issues/1) issue.

---

## Audit findings are submitted to this repo

Sponsors have three critical tasks in the audit process:

1. Weigh in on severity.
2. Respond to issues.
3. Share your mitigation of findings.

Let's walk through each of these.

## High and Medium Risk Issues

Please note: because wardens submit issues without seeing each other's submissions, there will always be findings that are duplicates. For all issues labeled `3 (High Risk)` or `2 (Medium Risk)`, these have been pre-sorted for you so that there is only one primary issue open per unique finding. All duplicates have been labeled `duplicate`, linked to a primary issue, and closed.

### Weigh in on severity 

Judges have the ultimate discretion in determining severity of issues, as well as whether/how issues are considered duplicates. However, sponsor input is a significant criteria.

For a detailed breakdown of severity criteria and how to estimate risk, please refer to [the judging criteria in our documentation](https://docs.code4rena.com/roles/wardens/judging-criteria#estimating-risk-tl-dr).

If you disagree with a finding's severity, **leave the severity label intact and add the label** `disagree with severity`, along with a comment indicating your opinion for the judges to review. You may also add questions for the judge in the comments.

### Respond to issues

Label each open/primary High or Medium risk finding as one of these:

- `sponsor confirmed`, meaning: "Yes, this is a problem and we intend to fix it."
- `sponsor disputed`, meaning either: "We cannot duplicate this issue" or "We disagree that this is an issue at all."
- `sponsor acknowledged`, meaning: "Yes, technically the issue is correct, but we are not going to resolve it for xyz reasons."

(Note: please *don't* use `sponsor disputed` for a finding if you think it should be considered of lower or higher severity. Instead, use the label `disagree with severity` and add comments to recommend a different severity level -- and include your reasoning.)

Add any necessary comments explaining your rationale for your evaluation of the issue. 

**Note that when the repo is public, after all issues are mitigated, wardens will read these comments; they may also be included in your C4 audit report.**

## QA and Gas Reports

For low and non-critical findings (AKA QA), as well as gas optimizations: wardens are required to submit these as bulk listings of issues and recommendations. They may only submit a single, compiled report in each category:

- **[QA reports](https://docs.code4rena.com/roles/wardens/judging-criteria#qa-reports-low-non-critical)** should include *all* low severity and non-critical findings, along with a summary statement.
- **[Gas reports](https://docs.code4rena.com/roles/wardens/judging-criteria#gas-reports)** should include *all* gas optimization recommendations, along with a summary statement. 

**For QA and Gas reports, sponsors are not required to weigh in on severity or risk level.** We ask that sponsors: 

- Leave a comment for the judge on any reports you consider to be particularly high quality. (These reports will be awarded on a curve.)
- Add the `sponsor disputed` label to any reports that you think should be *completely* disregarded by the judge, i.e. the report contains no valid findings at all.

## Once labelling is complete

When you have finished labelling findings, drop the C4 team a note in your private Discord backroom channel and let us know you've completed the sponsor review process. At this point, we will pass the repo over to the judge to review your feedback while you work on mitigations.  

## Share your mitigation of findings

*Note: this section does not need to be completed in order to finalize judging. You can continue work on mitigations while the judge finalizes their decisions and even beyond that. Ultimately we won't publish the final audit report until you give us the OK.*

For each finding you have confirmed, you will want to mitigate the issue before the contest report is made public.

### If you are planning a Code4rena mitigation review:
1. In your own Github repo, create a branch based off of the commit you used for your Code4rena audit, then
2. Create a separate Pull Request for each **High or Medium risk** C4 audit finding (e.g. one PR for finding H-01, another for H-02, etc.)
3. Link the PR to the issue that it resolves within your contest findings repo.

Most C4 mitigation reviews focus exclusively on reviewing mitigations of High and Medium risk findings. Therefore, QA and Gas mitigations should be done in a separate branch. If you want your mitigation review to include QA or Gas-related PRs, please reach out to C4 staff and let’s chat!

If several findings are inextricably related (e.g. two potential exploits of the same underlying issue, etc.), you may create a single PR for the related findings.

### If you aren’t planning a mitigation review

1. Within a repo in your own GitHub organization, create a pull request for each finding.
2. Link the PR to the issue that it resolves within your contest findings repo.

This will allow for complete transparency in showing the work of mitigating the issues found in the contest. If the issue in question has duplicates, please link to your PR from the open/primary issue.
