# Random Bug Walk results

This repository is the published experimental record of the public demo of [Random Bug Walk](https://github.com/Erik-1259/random-bug-walk), a hackathon prototype. Random Bug Walk plants a synthetic bug in a pinned copy of the open-source analytics app [Umami](https://github.com/umami-software/umami). Then, on fresh app copies, it checks whether Umami's own API test suite misses the bug while four added checks catch it, and has a model write a user-style bug report from the observed symptom.

It holds 6 runs under `runs/` and 1 release under `releases/`. The [results site](https://random-bug-walk.vercel.app) reads these files and presents them. Its [catalog](https://random-bug-walk.vercel.app/catalog) lists every run.

## How it is written

- One program writes `runs/` and `releases/`: the project's trusted publisher ([`packages/publisher`](https://github.com/Erik-1259/random-bug-walk/blob/main/packages/publisher/README.md)). It adds a run only after the run has finished, in one commit that adds `runs/<id>/` and nothing else. It adds a release under `releases/<id>/` only when the run that release names is already here, with the manifest hash the release pins. The same publisher also publishes this README.
- Before each push the publisher scans every file it publishes and the commit message, and publishes nothing if the scan blocks or cannot run. Pushes are fast-forward only.
- Every commit's author is `Random Bug Walk` with an empty email. The message is `chore(runs): publish <id>`, `chore(releases): publish <id>` or, for this README, `chore(readme): publish`. Each folder here was added by one commit, and no later commit has changed it (`git log -- <folder>` shows one commit).
- All six runs here completed. Factory attempts that failed or did not finish were not published here, so this is not a record of every attempt.
- Inside a run, `manifest.json` lists each withheld item and each redaction, so you can see what was left out.

## What is here

The table lists the records present at commit `5f4ec53` (Oct 13, 2026), oldest first. IDs are cut to 8 characters, and the links open the full folders.

| Folder | Kind | Outcome | Ran (UTC) | Commit | What it is |
|---|---|---|---|---|---|
| [`runs/5c625132`](runs/5c625132-0ab4-48c8-a7f7-d72729354e97) | factory root | completed | Oct 11, 16:57 to 18:04 | `74faf66` | Candidate attempt: kit check, observation, the writer's card and issue, exact-phrase checks and a 13-copy admission (verdict pass, blind spot demonstrated). No release names it. The site lists it as a diagnostic archive. |
| [`runs/0495237f`](runs/0495237f-9310-4472-885e-4728fddc1257) | factory root | completed | Oct 11, 22:26 to 23:38 | `5704025` | Candidate attempt with the same stages and the same admission result. **The released run:** release `319f3100` is built from it. |
| [`releases/319f3100`](releases/319f3100-9ecf-441d-b7af-a643af9ae4e5) | release | a person approved its issue (Oct 12) | created Oct 12, 17:52 | `d173ff2` | The validated example: the release record, the frozen issue, the code checks on it and the writer's two model calls. |
| [`runs/dd28e4f2`](runs/dd28e4f2-4de9-5215-a50e-2a627a7e0e74) | judge replay | completed | Oct 12, 17:58 to 18:07 | `c50202a` | Replay of release `319f3100` on 3 fresh copies (clean, planted, fixed). All 6 cells matched the release. |
| [`runs/dd8a971d`](runs/dd8a971d-6ee1-5b6b-bf68-da9cb21648f1) | judge replay | completed | Oct 12, 20:41 to 20:51 | `a7912e0` | Another replay of release `319f3100`. All 6 cells matched. |
| [`runs/9a403b66`](runs/9a403b66-4b4e-5327-895b-e893e42acdab) | judge replay | completed | Oct 13, 08:49 to 08:59 | `2d745d2` | Another replay of release `319f3100`. All 6 cells matched. |
| [`runs/8bdc650f`](runs/8bdc650f-6dce-4278-b9b8-d50431225b04) | factory root | completed | Oct 13, 12:02 to 13:06 | `5f4ec53` | Candidate attempt with the same admission result. It is the only run here that also ran the Tavily source searches and the Tavily docs extract. No release names it. The site lists it as a diagnostic archive. |

There are no other kinds of records. The kind and outcome of each run are in its `manifest.json` (`kind`, `outcome`) and `report.md`.

## Layout

### `runs/<id>/`

A factory run has 1,248 published files (about 13.6 MB) plus its manifest. A judge replay has 102 files (about 2.1 MB) plus its manifest.

| Path | What it holds |
|---|---|
| `manifest.json` | Every published file with its SHA-256 and size, plus one `withheld/<n>` entry for each withheld item. It does not list itself. |
| `report.md` | The run's kind, its outcome and a table of its jobs. |
| `generated/symptom.json` | Factory runs only. The observed symptom the issue writer was given: the fixture's events, the expected and observed daily counts, and `doc_excerpts`, passages from Umami's public API docs page. In `8bdc650f` the passages came from the Tavily docs extract. In the two earlier runs they are two sentences copied by hand from the same page. |
| `generated/card.json` | Factory runs only. The pattern card written by the model. |
| `generated/novelty.json` | Factory runs only. The result of three exact-phrase web searches (Tavily) for phrases from the frozen issue: `status`, `matching_urls` and `incomplete_calls`. The phrases are not published. |
| `results/<job>/request.json` | The job request: kind, policy, task revision and spend reservation. |
| `results/<job>/expected-trials.json` | The copies the job expected and the result each one should give. |
| `results/<job>/evidence.json` | Each copy's imported results. For admission and replay jobs it also holds the cells: each cell is a copy and a suite (`original` or `added`), with the tests run, the failing tests and whether that matched what was expected. |
| `results/<job>/decision.json` | For an admission job: the decisions on rules ADM-02 to ADM-06, the ADM-08 comparison, the rules not evaluated in this job, and the verdict. For other jobs, `outcome_verdict` is `null`. |
| `results/<job>/summary.json` | The controller's summary: times, each copy's phases and limits, and spend. |
| `results/<job>/<copy>/` | One app copy (`clean-01`, `planted-01`, `fixed-01`, `partial-01`, `stub-01`, ...): `trial-result.json`, `observations.json`, `artifacts.json` and `artifacts/`. |
| `.../artifacts/original/` | The run of Umami's original API suite (271 tests): the Playwright report, per-test outcomes and the suite manifest. |
| `.../artifacts/added/round-NN/` | One round of the four added checks (UTC, Los Angeles, Auckland, Kolkata): the Playwright report, plus each check's observation and HTTP response. |
| `.../artifacts/build/`, `readiness/`, `stop/`, `processes/` | Build, start and stop output, and the Umami and PostgreSQL logs. |
| `.../artifacts/phase-timings.json`, `diagnostics.json` | Phase timings and the driver's self-checks. |

The jobs in a factory run:

- **Kit check:** 5 clean copies. Each runs the original suite and the added checks (20 rounds in `clean-01`, 1 in the others).
- **Observe:** 1 planted copy, which runs the added checks once.
- **Admission:** 13 copies (1 clean, 5 fixed, 5 planted, 1 partial fix, 1 stub fix). Each runs the original suite once and the added checks once, except `fixed-01` and `planted-01`, which run them 20 times. The rules are in [`docs/admission-rules.md`](https://github.com/Erik-1259/random-bug-walk/blob/main/docs/admission-rules.md).

A judge replay has one job: 3 copies (clean, planted, fixed), each running both suites once.

### `releases/<id>/`

| Path | What it holds |
|---|---|
| `release.json` | The release record, as canonical JSON: the family and source fix, the complete task revision, the kit and controller images by digest, the run it is built from, the admission result, the person's approval of the issue, `calibration: "not_requested"`, the SHA-256 of every other file in the folder, and the key and SHA-256 of the frozen judge job (the judge job itself is kept private). |
| `issue.json` | The frozen bug report the model wrote, which a person approved. |
| `issue-check.json` | The code checks on that issue: structure, every number, internal identifiers and hint words. |
| `recordings/card-1.recording.json`, `recordings/issue-2.recording.json` | The writer's two model calls, with request and response. The model is `nvidia/Nemotron-3_5-Lightning`, served on Nebius Token Factory ([`packages/writer`](https://github.com/Erik-1259/random-bug-walk/blob/main/packages/writer/README.md)). |

### How the records link

- `release.json` names its run (`run.root_execution_id`, here `0495237f`), the SHA-256 of that run's `manifest.json` (`run.manifest_sha256`), the commit that added the run (`run.repository_commit`) and the admission job (`admission.execution_id`).
- Each replay's `results/<job>/request.json` names the release (`release_id`) and its task revision (`task_revision`). That value is the `sha256` of the revision in `release.json`.
- The admission's `request.json` carries the earlier, provisional task revision. The release's complete revision adds the frozen issue's hash and style, and the release tool checks that the two agree ([`tools/release`](https://github.com/Erik-1259/random-bug-walk/blob/main/tools/release/README.md), `revision_mismatch`).

### Labels that can mislead

- Every `summary.json` says `label: "development_evidence"`, `admission_claim: false` and `published: false`. The controller writes these same labels on every summary, because it never publishes or admits anything itself ([`tools/controller`](https://github.com/Erik-1259/random-bug-walk/blob/main/tools/controller/README.md)). The publisher copied these summaries here later and changed nothing in them except the provider-identifier redactions listed in the manifest. The admission result is in the admission job's `decision.json` and in `release.json`.
- In `generated/card.json`, code fills `id`, `provenance` and `shape`. The other fields, such as `bug_class`, `fault_shape` and `fidelity_tier`, are the model's own answer, which code checks against the card schema ([`packages/writer`](https://github.com/Erik-1259/random-bug-walk/blob/main/packages/writer/README.md), "Pattern card"). The shape's own label is in [`packages/shapes`](https://github.com/Erik-1259/random-bug-walk/blob/main/packages/shapes/README.md): `synthetic_transplant`, tier B.
- Amounts in `summary.json` and `request.json` are micro-US dollars on the project's spend ledger, priced from a pinned rate sheet that is not confirmed against the provider account ([`packages/envelope`](https://github.com/Erik-1259/random-bug-walk/blob/main/packages/envelope/README.md)). They are not invoices.

## How to verify the hashes

Files larger than 1 MiB would go to the project's public file store, and their manifest entries would carry a `public_uri`. The largest file here is 485,561 bytes, so every published file is in this repository. On Linux, `sha256sum` works in place of `shasum -a 256`.

```sh
git clone https://github.com/Erik-1259/random-bug-walk-results
cd random-bug-walk-results

# 1. Every file in a run against its manifest (prints nothing when all match; repeat for each run)
(cd runs/0495237f-9310-4472-885e-4728fddc1257 && jq -r '.entries[] | select(.outcome == "published" and .public_uri == null) | "\(.sha256)  \(.path)"' manifest.json | shasum -a 256 -c --quiet)

# 2. The release's files against release.json, and release.json itself
(cd releases/319f3100-9ecf-441d-b7af-a643af9ae4e5 && jq -r '.files[] | "\(.sha256)  \(.path)"' release.json | shasum -a 256 -c && shasum -a 256 release.json)
# The last hash should be 838c51516c9dc2a75490ae81839792a99b6d388fc46f1a23a2d2bc0fa312c8b7, the "Release SHA-256" on the site.

# 3. The release pins the run it was built from
jq -r '.run.manifest_sha256, .run.repository_commit' releases/319f3100-9ecf-441d-b7af-a643af9ae4e5/release.json
shasum -a 256 runs/0495237f-9310-4472-885e-4728fddc1257/manifest.json
git log -1 --format='%H %s' 5704025f63b0894b456c28364edc8212932bd737
```

## What is withheld or redacted

- **Withheld from each factory run:** the issue, its check report, the exact-phrase search records, the writer's recordings and the search recordings (5 entries). `8bdc650f` also withholds its source search records and its docs extract record (7 entries). The manifest lists each one as `withheld/<n>`, with outcome `withheld_private`, reason `private_material`, and no hash or size. For the released run, the release folder publishes the issue, its check report and the writer's recordings. The issues of the other two factory runs stay private.
- **Redacted:** provider resource identifiers (each app copy's sandbox name, container and resource ID) in `summary.json` files, replaced with `[redacted:provider_identifier]`. The manifest entry of each such file records the category and the count: 57 per factory run, 9 per replay.
- The publisher also replaces the values of `Authorization`, `Cookie` and similar header lines with `[redacted:auth_header]`. None of the manifests here records such a replacement.
- **Not in this repository:** the frozen judge job that replays run from. `release.json` gives its key and SHA-256.

## What this record is not

- **Not a finding about Umami.** The bug is planted on purpose by a deterministic code rewrite, not by a model: one date call on the PostgreSQL path of the pageview statistics query stops passing the selected time zone, so daily counts fall into UTC days. It is modeled on a public Umami fix (commit `e6f3f3b4`). Nothing was filed upstream. The clean copies run Umami's source at commit `ec0ff503` without the planted change.
- **A demonstration, not an evaluation.** It covers one app at one pinned commit, one bug shape (`DT-1.tz-arg`), one fixture (`umami-tz-arg-001`) and one release. The family is labeled `split: "public_demo"` and is public on purpose. It is not held out ([`packages/schema`](https://github.com/Erik-1259/random-bug-walk/blob/main/packages/schema/README.md), registry).
- **No difficulty or solve-rate claim.** `release.json` says `calibration: "not_requested"`, and no solver attempts are recorded here.
- **Not every attempt.** Failed and incomplete factory attempts are not published here.
- **No replay verdict field.** A replay's records show the six cells and whether each matched the release, and the run's outcome is `completed`. Its `decision.json` has `outcome_verdict: null`.

## Links

- Results site: <https://random-bug-walk.vercel.app> (the validated example) and <https://random-bug-walk.vercel.app/catalog> (every run)
- Code: <https://github.com/Erik-1259/random-bug-walk>. Useful starting points are [`packages/publisher`](https://github.com/Erik-1259/random-bug-walk/blob/main/packages/publisher/README.md) (how this repository is written), [`tools/release`](https://github.com/Erik-1259/random-bug-walk/blob/main/tools/release/README.md) (staging, the release record, the judge job), [`docs/admission-rules.md`](https://github.com/Erik-1259/random-bug-walk/blob/main/docs/admission-rules.md) and [`packages/umami-fixture`](https://github.com/Erik-1259/random-bug-walk/blob/main/packages/umami-fixture/README.md) (the fixture and the four added checks).
- Umami: <https://github.com/umami-software/umami>
