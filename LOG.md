## TASKS LOG

## 2026-04-05
1. Neutral-only monthly UN AI voting
- Completed: added a neutral-only AI voting pass for `je_un_votations` so each month only still-neutral AI countries compare the current UN law `L0` against the proposal `L`, then switch to support if `V(L) - V(L0)` beats `2 * months_remaining + 8`, or against if the reverse gap beats the same threshold.
- Updated: `common\journal_entries\ztr_je_un.txt`, `common\scripted_effects\ztr_un_scripts.txt`, and `common\scripted_effects\ztr_un_law_opinion_functions.txt`.
- Verified: checked that the new JE hook runs before `ztr_un_vote_update_results` in both the JE immediate block and monthly pulse, and that the neutral-only iterator excludes already-committed support/against votes.
2. Yearly AI UN proposal coordinator
- Completed: added a yearly hidden UN AI proposer flow that runs from `on_yearly_pulse_country`, selects two AI permanent members as `ztr_un_potential_proposer_scope_1` and `ztr_un_potential_proposer_scope_2`, lets the first one roll a random proposal, and uses the second one as a fallback evaluator for the same extracted proposal if the first path does not start a vote.
- Updated: `common\on_actions\ztr_on_actions.txt` and `events\international_organizations\un_events.txt`.
- Verified: confirmed the yearly coordinator only fires from the top-ranked country context, remains hidden, and stops the fallback once `ztr_un_vote_in_progress` is set by a successful proposal.
3. AI proposal evaluation/start helpers
- Completed: added `ztr_un_set_possible_law_as_considered`, `ztr_un_set_current_law_as_considered`, `ztr_un_ai_try_start_possible_vote`, and `ztr_un_country_can_propose_possible_law` so AI permanent members can compare `V(L0)` against `V(L)` and only launch proposals that match the same subject-level availability rules as the player-facing UN menus.
- Updated: `common\scripted_effects\ztr_un_law_opinion_functions.txt` and `common\scripted_triggers\ztr_un_law_opinion_triggers.txt`.
- Verified: checked that the current-law mapping covers all eleven active UN subjects, that institution proposals still respect their tech gates, and that the start-vote helper sets `ztr_un_vote_subject`, `ztr_un_vote_proposal`, and `ztr_un_vote_proposer` through the existing UN vote pipeline.
4. UN AI law valuation refactor
- Completed: refactored `ztr_un_value_law` toward the consistent `V(L)` model by introducing `ztr_un_value_law_prepare_country_context`, normalizing liberty and equality into reusable `[-10, 10]` range variables, and switching the governance secret-role scoring to absolute considered-law value instead of a built-in `current -> proposed` delta.
- Updated: `common\scripted_effects\ztr_un_law_opinion_functions.txt`.
- Verified: confirmed the old `ztr_is_democratic`, `ztr_is_not_democratic`, `ztr_is_full_democratic`, `ztr_un_value_law_current_un_role`, and `ztr_un_value_law_un_role_delta` references are gone from the evaluator, while the new absolute-score comments for voting model, veto, and member selection resolve in the file.
5. Expanded country-context heuristics for UN voting AI
- Completed: added granular AI-style country context for liberty, equality, literacy, development stage, demographic stage, government structure, market orientation, interest-group blocs, and imperial stake from subjects or colonial laws, then reused those values across governance, decolonization, slavery, children’s rights, women’s rights, IMF, WTO, ICC, and UNESCO branches.
- Updated: `common\scripted_effects\ztr_un_law_opinion_functions.txt`.
- Verified: checked that the new context effect is called at the top of `ztr_un_value_law`, that temporary variables are cleaned up at the end of the function, and that script braces remain balanced (`1206` opening and `1206` closing braces).
- Follow-up: no in-game AI vote simulation has been run yet, so the next useful check is whether countries now produce sensible `V(L0)` vs `V(L)` gaps across a few live UN proposals.
