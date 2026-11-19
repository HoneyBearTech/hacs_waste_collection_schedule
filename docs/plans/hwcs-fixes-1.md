# Plan: hwcs-fixes-1

## What Sean asked for
"Start the next repo on the shortlist": fix real open problems in hacs_waste_collection_schedule and send each fix to the original project (mampfes/hacs_waste_collection_schedule) as its own pull request.

## What we found
- **21 open issues; four are real, fixable now, and not already being fixed by someone else.** Each was confirmed against the council's live website. The others are already covered by open pull requests (Wolverhampton, Germersheim), need a bigger rewrite (Erlangen-Höchstadt, Reinis), are blocked by the website (Malta), are support questions or feature requests, or are only on the maintainers' 3.0 beta branch.
- **The project has two lines:** `master` (the current release) and `release/3.0.0` (a beta being rebuilt by the maintainers). Its guidelines say bug fixes for the current release go to `master`. Two of the bugs below were already fixed on the beta branch but never on `master`.
- **How outside fixes look:** small, one file per fix (the council's source file), titled `fix(<source>): ...`, tested by running that source's own live test addresses. Generated files (README, info.md, sources.json, translations) are never edited by hand.
- **Baseline:** all 195 project tests pass on `master`.

## What we'll change
Each fix is one commit touching only that council's source file (and its docs page only if what users enter changes). Each is checked by running the source's own test addresses against the live council website, before and after.

1. **#8235 Hastings (New Zealand):** every rubbish and recycling date shows on a Monday.
   - **Cause:** the code turns a day name like "Thursday" into a weekday number in a way that always gives Monday.
   - **Change:** look up the weekday from its name. One of the test addresses also matches two streets ("200" and "1200 Heretaunga Street East"); fix the address lookup or the test address so it picks the exact match.
   - **Check:** live test addresses all pass, and the Fitzroy Avenue address shows rubbish on Thursdays and recycling on Wednesdays, as the council's own site does.
2. **#8222 Karlsruhe (Germany):** the council moved its calendar page, so the source gets a web page instead of a calendar file.
   - **Change:** point it at the new address (`akal.php`, without the year) and drop the per-year loop it no longer needs.
   - **Check:** all 5 live test addresses return dates.
3. **#8217 AWIDO (Germany, many councils):** the source crashes when a council's calendar uses a waste type code it doesn't know (`GTS`, `PG` for one council).
   - **Change:** skip codes that aren't in the council's list, with a debug log line, so the known waste types still show. This matches what the maintainers did on the beta branch.
   - **Check:** the affected council (`azv-hef-rof`, Neuenstein) returns its dates live, and the existing test council still passes.
4. **#8219 Wanneroo (Australia):** the council replaced its address search, so every address lookup fails.
   - **Change:** use the council's two new lookups (address, then bin day), the same way the maintainers already did on the beta branch. Like the beta branch, it shows the next date for each bin (general, recycling, garden).
   - **Check:** all live test addresses return dates; a wrong address gives a clear "not found" error.
5. **For every fix:** the project's checks pass (tests, ruff lint and formatting, the pre-commit hooks that apply to the changed files), and each commit applies on its own to the latest `master`.
6. **Send upstream, only after the final approval on this PR:**
   - Each fix gets its own clean branch from the original project's latest `master`, holding only that fix. The plan file never goes upstream.
   - One pull request per fix to mampfes/hacs_waste_collection_schedule, from Sean's account, using the project's pull request template. Written as Sean working alone, with `Fixes #<n>`.
   - For Hastings and Karlsruhe, the description says the same bug exists on `release/3.0.0` and offers a follow-up there.
   - Draft titles:
     - "fix(hastingsdc_govt_nz): use the real collection weekday instead of Monday"
     - "fix(karlsruhe_de): use the new calendar address without the year"
     - "fix(awido_de): skip waste type codes missing from the council's list"
     - "fix(wanneroo_wa_gov_au): use the council's new address and bin-day lookups"
   - The full title and description of each is shown here first.

## Who's on it
Researcher (done), Engineer, QA, Security Officer, Auditor, plus a plain-English check before the final approval.

## Risks
- **Wanneroo is the biggest change:** most of that source file is replaced, and it will show only the next date per bin rather than projecting many weeks ahead. That's how the maintainers chose to fix it on the beta branch, so it's consistent with their direction.
- **AWIDO skips unknown waste types:** for the affected council, two waste types (codes `GTS` and `PG`) won't show until someone adds them to the council's list. Today the whole calendar fails, so this is still an improvement.
- **Council websites can change again** at any time; the checks only prove the fixes work today.
- **Overlap with the beta branch:** the maintainers may prefer to fix only the beta branch. Each pull request is small, so it's easy to accept or drop.

## Out of scope
- Fixes on the 3.0 beta branch (including the Home Assistant warning in #8230), offered as follow-ups only.
- Erlangen-Höchstadt (#8226) and Reinis (#8221): need a rewrite against new websites.
- Malta (#8231): the website blocks automated requests.
- New councils (#8228 Lorient) and feature requests.
- Issues already covered by open pull requests (#8236, #8218).
- Fork-only files: no guidelines file, badges or notes go into the upstream pull requests.
