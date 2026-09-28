# Live verification record — 2026-09-26 UTC

[한국어](live-validation.ko.md) · [Workshop](../README.md) · [Compatibility](compatibility.en.md)

**Follow-up completed on 2026-09-27 (KST):** portal checks, human confirmation, red teaming, private CI, a zero-error automatic run per language, and approved foundation cleanup are recorded in [the follow-up section](#follow-up-20260927). The initial attempt below remains historical evidence; its failed runs were not relabeled.

**The English and Korean CLI/SDK core workshops were deployed and executed in separate Azure environments, with 48 real responses and 48 verified traces per language.** This is a new rehearsal, not a relabeling of the September 23 recordings.

**Not every guide surface was verified during the initial attempt:** interactive portal checks, a human review, organization-approved red teaming, paid GitHub CI, and final resource-group deletion were unavailable without further user participation or approval. Continuous evaluation retained intermittent judge authentication errors after three automatic runs. Those paths were not counted as completed at that stage. Rehearsal-owned temporary objects and schedules were removed and independently checked.

## Scope and unchanged environment

Two dedicated, ownership-tagged Sweden Central environments were created using the guide: Foundry account/project, Search, Application Insights, Log Analytics, three fixed candidate deployments, and a separate judge/planner. Existing shared resources and the unrelated global Azure CLI sign-in were preserved.

The source baseline was `7c9f608`. The SDK remained `azure-ai-projects==2.3.0`; all 90 local versions in `requirements.lock.txt` still matched after local azd execution. Runtime recovery patches were retained separately from each original source manifest. No dependency upgrade was used to solve the observed failures.

The live rehearsal ran on macOS with Python 3.13.15, Azure CLI 2.86.0, azd 1.34.0, `microsoft.foundry` 1.0.0-beta.2, and installed `azure.ai.agents` 1.0.0-beta.10. It does not establish live Windows/WSL or Linux deployment coverage.

Review records explicitly say `reviewer_type=assistant`. The saved baseline trace was reused by V2, but this is **not evidence of a human sign-off**. All values below come from saved run outputs, not desired outcomes.

## Core results

| Language / stage | Real responses | Business passes | Groundedness passes | Relevance passes |
|---|---:|---:|---:|---:|
| English V1 dev | 18 | 0/18 | 18/18 | 17/18 |
| English V2 dev | 18 | 17/18 | 18/18 | 17/18 |
| English frozen-V2 holdout | 12 | 12/12 | 12/12 | 12/12 |
| Korean V1 dev | 18 | 0/18 | 18/18 | 16/18 |
| Korean V2 dev | 18 | 18/18 | 18/18 | 16/18 |
| Korean frozen-V2 holdout | 12 | 12/12 | 12/12 | 12/12 |

Both cohorts used immutable hosted versions 1 and 2, the same dev set before/after, concurrency 4, fixed evaluator definitions, and first-use holdout after freezing V2. Each `verify` confirmed 48 distinct, successful, unsampled traces and the original reviewed baseline lineage. All six business gates passed per cohort; **`production_release_approved=false` remains unchanged**.

English `improved-sol-D02` still returned `not_allowed` where the fixed contract requires `needs_approval`. That failure was retained. Native relevance also marked down justified D04 deferrals. Retrieval context differed in some paired cases, so these are end-to-end observations, not isolated model rankings.

## Advanced paths

| Path | English evidence | Korean evidence |
|---|---|---|
| Level 2 suite | All 9 criteria on 36 saved responses | All 9 criteria on 36 saved responses |
| Code evaluator, V1 → V2 | 0/18 → 17/18 | 0/18 → 18/18 |
| Rubric with evidence, V1 → V2 | 6/18 → 18/18 | 7/18 → 18/18 |
| Rubric without evidence, V1 → V2 | 0/18 → 10/18 | 1/18 → 9/18 |
| Judge agreement and insights | Actual disagreement tables and failure clusters read | Actual disagreement tables and failure clusters read |
| Generated rubric on V2 | 17/18 passes; manual rubric 18/18 | 18/18 passes; manual rubric 18/18 |
| Corrected synthetic stress run | 15 valid rows; 7 quality failures | 15 valid rows; 2 quality failures |
| Agent-target evaluation | 18 new responses / 18 traces; business 18/18 | 18 new responses / 18 traces; business 18/18 |
| Trace evaluation | All 18 original V2 traces; four criteria 18/18 | All 18 original V2 traces; four criteria 18/18 |
| Continuous evaluation | Three automatic 20-trace runs inspected; final run still has 8 judge execution errors | Three automatic 20-trace runs inspected; final run still has 3 judge execution errors |

The final business gate exited **0** for both cohorts. The composite gate exited **1**, correctly blocking incomplete continuous-evaluation evidence and the unexecuted red-team signal. No waiver was applied.

The schedules were not reset to hide failures. English invalid evaluator-result counts were **40 → 14 → 8**; Korean counts were **40 → 16 → 3**. The final runs started at 15:07 and 15:06 UTC, respectively. Required project-identity roles and inference data actions were verified, but the remaining intermittent `AuthenticationError` was not fully resolved. These are evaluator execution errors, not valid low quality scores. Manual evaluation under the user's credentials was not substituted for the scheduled identity.

These observations are separate from the primary 48 responses. The canceled Korean Astra target run and the two original underfilled stress runs remain in their respective attempt histories; successful data was not discarded for better scores.

The generated English rubric noticed the D02 decision error but still passed it at about 0.615 with threshold 0.5. It also failed a valid D04 deferral at about 0.456. In one inspected synthetic case, the target correctly treated Chicago as outside the supplied South Korea policy, while a judge without that policy called the classification wrong. Generated criteria and generic scores are not substitutes for the business contract or domain review.

## Problems reproduced and corrected

| Observed problem | Correction |
|---|---|
| Search creation returned `FailedIdentityOperation` before a resource was recorded | Read-only `search-status` now distinguishes absent, recorded, and unrecorded resources. A confirmed absent failed creation was retried under the same name; identity/authentication was not weakened |
| Level 2's code-evaluator suite failed on account-level `evals/write`, despite Level 1 working | Added optional `user-evaluation` preparation with account-scoped Foundry User and corrected both guides |
| The suite error omitted the service's actual cause | The error and service counts are persisted and shown; the original failed run was retained before retry |
| A 15-question stress request returned 13 rows but printed completion | Exact row-count and duplicate checks now reject underfilled/extra output, including cached stress results. Both original outputs were archived; one separate corrected run per language produced 15 rows |
| Synthetic target used an undocumented `developer` message for this preview flow | Aligned the new synthetic target with the documented `system`-only message contract; no claim that this alone caused the row-count change |
| Schedule creation lacked project-identity access, then scheduled judges lacked inference access | The initial narrower assignments were insufficient. The current `project-evaluation` grants the officially documented **Foundry User on the parent Foundry account**; see the follow-up for verification and remaining interpretation limits |
| A scheduled run said `completed` / 20 overall passes despite 40 judge execution errors | The CLI now saves and validates each row, exposes `sample.error`, records `results_complete`, and blocks incomplete or legacy-unverified evidence in the composite gate |

The Korean local smoke initially returned 404 after readiness 200; the listener/route was checked, and one smoke retry succeeded. Its exact transient cause was not established. Korean baseline telemetry initially exposed only 4/18 traces; the original traces later became complete without recollection, rescoring, or weaker filters.

One Korean agent-target run remained in progress without published rows for over an hour. Its original state was saved, only that Astra run was canceled and confirmed terminal, and `--retry-failed` replaced only Astra. The successful Sol/Luna runs were retained.

## Evidence, limitations, and cleanup

Local evidence is under `.workshop/en-20260926-live/` and `.workshop/ko-20260926-live/`: source manifests/amendments, setup and command logs, response matrices, evaluator outputs, telemetry, attempt archives, and `workshop-report.txt`. These private artifacts are Git-ignored and are not included in the public repository.

The published credential-free [validation workflow](https://github.com/junwoojeong100/foundry-evaluation-labs-v0.8/actions/runs/36237697845) passed on the source-baseline commit. The new recovery changes passed the local offline suite; this does not claim that an unpushed change has run in GitHub Actions.

The cleanup plans were checked against the unchanged ownership records. For **each** cohort, `check-cleanup` confirmed the agent was absent, plus **3 candidate deployments, 3 Search objects, 3 temporary role assignments, 1 schedule, 3 custom evaluators, and 3 generated/artifact datasets** were removed. Foundry, Search, Application Insights, the auxiliary model, and local evidence were preserved. There are no remaining hourly schedules from this rehearsal.

**At the end of this initial attempt, the two foundation groups were retained because deletion approval was unavailable.** They were subsequently deleted with approval during the follow-up below. Existing/shared groups were not deleted or modified as a shortcut.

No portal check, human review, red-team scan, paid cloud CI, or whole-group deletion is claimed as verified for that initial attempt. Its boundaries remain part of the historical result.

<a id="follow-up-20260927"></a>

## Follow-up — approved remaining work

The user approved the remaining work and confirmed the two displayed original D01 responses: their amounts and `allowed` decisions were correct, but they cited titles rather than `TRAVEL-2026`. Separate human-confirmation files preserve the original trace IDs; earlier `assistant` review records remain unchanged.

New, separately named hosted agents reused the two retained foundations. SDK 2.3.0, the candidate model versions, prompts, policies, evaluator definitions, and original response files were preserved. Follow-up traffic is not added to either original 48-response study.

### Portal verification

| Surface | Actually checked |
|---|---|
| Sign-in and project selection | Requested account/tenant; classic-to-New-Foundry project and account selection |
| Original primary evaluations | All six reports; exact native pass counts and 18/18/12 distinct rows per language across pagination |
| Level 2 and generated rubrics | Both nine-criterion runs and the manual/generated comparison in both languages match saved counts |
| Models and knowledge | Exact model/version contracts, deployment `Succeeded`, KB source `Active`, populated retrieval instructions |
| Hosted agents | Immutable versions 1 and 2 in each Playground |
| Traces | Actual follow-up trace dialogs with 11 spans, `learning_loop.answer`, `foundry_iq.retrieve`, and the expected model call |
| Monitor | `Last Day` charts populated; observed English `completed: 87`, 215.9K tokens and Korean `completed: 39`, 108.3K tokens during CI |
| Red team | Explicit ASR columns and six outcome rows match the SDK; a failed attack outcome denotes a successful attack |
| Successful scheduled runs | 20 distinct trace rows over two pages and all three criterion counts, cross-checked against SDK output |

The Monitor readings are point-in-time follow-up totals that include CI/seed traffic, not replacements for the original cohort's totals. The restored agents' trace views were not misrepresented as the deleted original agents' views.

### Scheduled evaluation: result and limits

The confirmed permission correction is **Foundry User for the project managed identity at the parent Foundry-account scope**, as stated in the [official RBAC minimum assignments](https://learn.microsoft.com/azure/foundry/concepts/rbac-foundry#minimum-role-assignments-to-get-started). A project-only Foundry User grant plus direct OpenAI access was not the supported substitute. No Owner role, key authentication, or SDK upgrade was used.

| Verified automatic configuration | Trace rows | Relevance | Task adherence | Indirect attack | Execution errors |
|---|---:|---:|---:|---:|---:|
| Korean follow-up, 20:24:54 UTC | 20 | 20/20 | 20/20 | 20/20 | 0 |
| English isolated judge, 20:53:03 UTC | 20 | 20/20 | 20/20 | 20/20 | 0 |

**The English result needs qualification.** Its older shared-judge configuration still produced intermittent generic `AuthenticationError` results after role propagation. A separate fresh definition using that same deployment also retained one error. A further controlled configuration used a dedicated deployment of the **same `gpt-5.4-mini` / `2026-03-17`, Global Standard capacity 100, unchanged RAI policy**, with the same target agent and project identity. That automatic run completed with 20 distinct traces and 60 valid results.

All previous failed definitions, runs, and outputs remain in the private evidence. The successful isolated run and the preceding failed run shared only 8 of their 20 automatically selected traces, so this is **not a paired causal experiment**. It demonstrates a working separately configured path; it does not prove that deployment separation alone fixed the service, retroactively repair the old shared deployment, or guarantee that intermittent errors can never recur. Never turn a generic authentication error into a valid low score or retry valid grades for a better result.

Prepare the documented identity roles before creating evaluation definitions. If an existing environment still fails, preserve its configuration and error evidence and have the environment owner investigate or run a separately identified, approved isolation check. Do not silently change a running study's judge or clear `continuous.json` to make it look successful.

### Red team and paid GitHub CI

The standalone model scans returned English-environment ASR **2/6** and Korean-environment ASR **0/6**. The first Korean scan omitted a risk category despite service status `completed`; its metadata was preserved before one separate complete retry. These are small model-target scans, not proof of the RAG agent's safety or language-dependent performance.

An approved private repository executed the actual release workflow on GitHub-hosted runners, using OIDC with a temporary user-assigned identity: subscription Reader and Foundry User only on the two verification accounts.

| CI cohort | Primary responses / traces | V1 → V2 business passes | Holdout | Separate agent-target rows | Gate result |
|---|---|---|---|---:|---|
| English | 48 / 48 | 0/18 → 18/18 | 12/12 | 18 | Correctly blocked: 2/6 red-team attacks succeeded |
| Korean | 48 / 48 | 0/18 → 18/18 | 12/12 | 18 | Passed with the template's explicit continuous-evaluation waiver |

Both `evaluate` jobs completed, verified real evidence, and removed their own custom evaluators. The English workflow's red gate is a valid safety finding, not an execution failure that was rerun for green. Continuous evaluation is waived **only within this independent CI workflow** and was exercised separately with the actual project identity.

CI is a separate execution over the same published teaching `dev`/`holdout` sets, not new untouched validation data.

CI now uses `feedback --reviewer automation` when copying prior review context onto new responses. This avoids falsely labeling new CI answers as freshly human-reviewed; the manual CLI default remains `human`.

### Final cleanup and retained evidence

The four follow-up schedules, two restored agents, six candidate deployments, six Search objects, and their run-owned roles were removed and checked before deleting the foundations. The temporary CI identity, its federated trust, and all three recorded CI role assignments—including the subscription-level Reader assignment—were removed.

After ownership/inventory checks and explicit approval, **both dedicated Azure resource groups were deleted and independently confirmed absent**, including the old and isolated judge deployments, Search, and monitoring resources. The pre-existing shared environment and unrelated global Azure CLI context were preserved. Earlier `check-cleanup` files describe the intermediate boundary when foundations still existed; they were not rewritten after group deletion.

The private CI repository is archived read-only, with both run artifacts also downloaded locally. Local evidence remains under `.workshop/`, including original/follow-up source manifests, configuration-specific failed and successful runs, human confirmations, portal verification, CI artifacts, and deletion records. Raw artifacts are not committed to this public repository. No production release approval is implied.
