# TakeMeter

> ### 👋 Start here
>
> **New to this repo? Read [RUNNING.md](RUNNING.md) first** — setup, the
> notebook, the baseline, and what to do when something breaks.
>
> Once `python test.py` passes:
>
> ```bash
> head -5 data/practice_labels.csv     # the shape your labels.csv needs
> ```
>
> Then open `takemeter.ipynb` **in this folder** — in VS Code, or with
> `jupyter notebook` if you prefer. Pick the kernel: the `.venv` inside this
> project. Run section 1, which reports the hardware you'll be training on.
> Everything else waits until you have data.
>
> Nothing to upload, nothing to connect, no accounts and no keys. The notebook
> runs on your machine and writes next to your code.
>
> **The rest of this file is your submission.** Fill it in as you go.

---

<!-- ─────────────────────────────────────────────────────────────────────────
     HOW TO USE THIS FILE

     Unit 5 asks for the first five sections. Unit 6 adds the five below them.

     Everything is pasted as TEXT. No screenshots, no images.

     ⚠️ The confusion matrix especially. The notebook prints one as a markdown
     table, ready to copy. A screenshot of a matrix earns nothing. Paste the
     table.
     ───────────────────────────────────────────────────────────────────────── -->

<!-- ═══════════════════════ UNIT 5 — THE BUILD ═══════════════════════ -->

## What This Does

<!-- Your community, and what your classifier sorts posts into. Three or four
     sentences. -->

TakeMeter analyzes posts and comments from an online distance running forum (reminiscent of communities like r/running and r/RunningCirclejerk). It classifies forum posts into three functional categories: `analysis` (arguments backed by load-bearing checkable facts), `hot_take` (confident assertions made without supporting evidence), and `reaction` (immediate, emotional or sensory responses to recent training and races). By distinguishing structured training insight from hot takes and post-run venting, the classifier helps readers and moderators quickly surface substantive running discussions.
---

## Label Taxonomy

<!-- Each label: a one-sentence definition and two real examples from your
     reading. Then your decision rule for the hardest boundary.

     The decision rule is worth a point on its own and it's the thing most
     people leave out. Every taxonomy has a hardest boundary. Name yours. -->

### `analysis`

**Definition:** Makes an argument backed by a specific, load-bearing checkable fact (like a race split, date, mileage number, or direct comparison).

**Example 1:**
> *"Looked at my last three build-ups. The one where I capped long runs at 2 hours had my best race by 4 minutes, despite the lowest weekly mileage of the three. Time on feet past 2 hours was costing me the next four days."*

**Example 2:**
> *"My Garmin insisted my training was 'Unproductive' for 6 weeks straight. Yet my 10k time trial dropped from 44:10 in October to 42:30 in December on identical weekly volume of 45 miles. The algorithm is penalizing high-heat runs, not actual fitness."*

### `hot_take`

**Definition:** A confident, subjective claim asserted without supporting data or reasoned argument.

**Example 1:**
> *"Super shoes have ruined amateur racing. Times mean nothing now and everyone knows it."*

**Example 2:**
> *"Wearing a hydration vest for a 5k parkrun should result in an immediate 6-month ban from running stores."*

### `reaction`

**Definition:** An immediate, emotional or sensory response to a recent event, focused on the writer's personal experience rather than an argument.

**Example 1:**
> *"Just got back from the long run and I cannot feel my hands. Worth it though."*

**Example 2:**
> *"Just got smoked by an 80-year-old grandfather wearing sandals and khaki shorts. Deleting Strava now."*

### The hardest boundary

**Which two labels:** `analysis` vs. `hot_take`

**The decision rule I used every time:**
Does the checkable fact actually carry the argument? If a number or stat is just tossed in as decorative flair on a conclusion the runner already arrived at (e.g. *"Super shoes ruined everything, times are down 3%"*), it is still a `hot_take`. It only counts as `analysis` when the specific data point does the heavy lifting to prove the claim.



---

## The Dataset

<!-- Where you collected from, how you labelled, your counts, and three hard
     cases. -->

**Where the posts came from:**
The dataset consists of 200 posts from an online distance running forum:
- 60 starter posts from the provided sample dataset (`data/practice_labels.csv`).
- 140 synthetic posts generated using Gemini 3.8 Flash, specifically emulating running discussion boards and Reddit communities (e.g., r/running, r/RunningCirclejerk). Generating these posts kept the project moving forward, as sourcing raw comments from live forums repeatedly turned into an exercise in getting distracted reading and interacting with the threads.

**How I labelled them:**
- The initial 60 posts retained the provided starter labels (`provided practice data`).
- The 140 generated posts were pre-labelled by Gemini 3.8 Flash according to the taxonomy constraints, and subsequently reviewed and verified against the decision rules. Borderline examples were specifically tagged in the CSV's `note` column.

**Counts per label:**

| Label | Count | Share |
|---|---|---|
| `analysis` | 67 | 33.5% |
| `hot_take` | 67 | 33.5% |
| `reaction` | 66 | 33.0% |
| **Total** | 200 | 100% |

**Three hard cases**

<!-- Any post that made you pause: what it was, which two labels it could have
     been, and what you chose. These are worth more than the easy 190. -->

**1.**
> *The post:* "The women's field here has grown 60% in five years while the men's is flat. That's the actual story of this race and nobody writes about it."
>
> *Could have been:* `hot_take`
>
> *I chose, because:* `analysis`. The cynical, editorializing tone ("nobody writes about it") sounds like a hot take, but the specific, checkable 60% vs. flat comparison is load-bearing and carries the argument.

**2.**
> *The post:* "Nobody needs a gel before 90 minutes. The whole fuelling industry is built on people panicking."
>
> *Could have been:* `analysis`
>
> *I chose, because:* `hot_take`. Citing "90 minutes" gives the appearance of a checkable physiological threshold, but zero supporting data or physiological reasoning is provided—it remains an unargued, confident claim.

**3.**
> *The post:* "Missed my goal by eleven seconds. Eleven. I'm going to be thinking about that for a month."
>
> *Could have been:* `analysis`
>
> *I chose, because:* `reaction`. Even though "eleven seconds" is a concrete, checkable metric, it is not functioning as evidence in an argument. It is strictly an expression of immediate personal heartbreak following a race.


---

## The Training Run

<!-- Your starting model, your settings, and anything you changed and why. -->

**Base model:** `distilbert-base-uncased`

**Settings:** 3 epochs, learning rate 2e-5, batch size 16, seed 42, max sequence length 128.

**Anything I changed from the defaults, and why:**
Kept the starter defaults as they provided solid convergence on the 200-item dataset and ran comfortably within memory limits on CPU.

**Split sizes:**
Stratified 70/15/15 split:
- **Train:** 140 posts (~47 per label)
- **Validation:** 30 posts (10 per label)
- **Test:** 30 posts (10 per label: 10 `analysis`, 10 `hot_take`, 10 `reaction`). No label had fewer than 8 examples in test.

---

## How I Used AI

<!-- Two specific moments — what you asked, what came back, what you changed.

     ⚠️ Plus disclosure of any pre-labelling. If you had a model pre-label a
     batch and then read and corrected every one, say that. It's an allowed
     workflow and disclosing it costs you nothing. Not disclosing it is the
     problem. -->

**Moment 1**

- *What I asked for:* I asked AI to generate 140 additional distance running forum comments with high variability—specifically requesting Reddit running tropes, humor, and memes (such as Garmin recovery roasts, Zone 2 jokes, and super shoes)—pre-labelled into `analysis`, `hot_take`, and `reaction` to reach the required 200-post dataset size.
- *What came back:* 140 synthetic forum comments pre-assigned to the three classes, balanced roughly equally (47 `analysis`, 47 `hot_take`, 46 `reaction`).
- *What I changed:* I reviewed and verified the generated posts and their proposed labels against the taxonomy decision rules (confirming that `analysis` numbers were load-bearing rather than decorative), and explicitly tagged ambiguous edge cases in the CSV's `note` column.

**Moment 2**

- *What I asked for:* I asked for suggestions to formulate my 5 acceptance criteria in `criteria.md` with measurable numbers and defensible rationales.
- *What came back:* An initial draft specifying technical evaluation metrics (e.g., Macro F1, per-label F1 floors) and referencing internal starter scripts (`python agreement.py`).
- *What I changed:* I revised the criteria to be described in plain, intuitive terms from the perspective of an end-user or moderator evaluating practical classifier reliability, replacing technical jargon like F1 and specific script names with accessible concepts (e.g., per-category accuracy, dataset balance limits, reviewer agreement, and baseline improvement).

**Pre-labelling disclosure:**
Of the 200 posts in `labels.csv`, 60 posts came pre-labelled from the starter dataset (`provided practice data`). The remaining 140 posts were generated and pre-labelled by Gemini 3.8 Flash (`synthetic, reviewed`), followed by manual review, validation against the taxonomy definitions, and hard-case annotation in the `note` column.


<!-- ═══════════════════════ UNIT 6 — THE TEST ═══════════════════════

     Don't fill these in during unit 5.
     ═══════════════════════════════════════════════════════════════════ -->

---

## Baseline vs. Trained

<!-- Both models on the same posts. `python baseline.py --trained results.json`
     prints this table for you. -->

| Measure | Baseline | Trained | Difference |
|---|---|---|---|
| Overall accuracy | 0.800 | 0.933 | +0.133 |
| Macro F1 | 0.800 | 0.931 | +0.131 |
| F1 — `analysis` | 0.800 | 0.889 | +0.089 |
| F1 — `hot_take` | 0.778 | 0.952 | +0.175 |
| F1 — `reaction` | 0.824 | 0.952 | +0.129 |

**What I predicted before I looked:**
<!-- Milestone 1 asks you to write this BEFORE seeing the trained numbers. A
     prediction made afterwards isn't one. -->
I'm predicting that the trained model might do better at hot takes as even to
a person unfamiliar with sarcasm, they might take such comments seriously.

**What the gap actually means:**
<!-- If the baseline matched your trained model, your fine-tuning added
     nothing — and that is a real finding, not a failure. Say it plainly. -->
The training actually did help improve the F1 scores for hot_take, increasing
it by 0.175, as well as improving the analysis and reaction scores by 0.089
and 0.129 respectively. As predicted, training allowed the model to more 
accurately detect the nuances of hot takes, such as the use of sarcasm.

**Test with bare label names:**
| Measure | Baseline | Trained | Difference |
|---|---|---|---|
| Overall accuracy | 0.667 | 0.933 | +0.267 |
| Macro F1 | 0.676 | 0.931 | +0.256 |
| F1 — `analysis` | 0.824 | 0.889 | +0.065 |
| F1 — `hot_take` | 0.615 | 0.952 | +0.337 |
| F1 — `reaction` | 0.588 | 0.952 | +0.364 |
These results show that having label definitions can help improve baseline 
model accuracy, but not to the extent that combining it with training does.

---

## Run Log — Before

<!-- Five criteria across three seeds. The notebook's section 6 prints the
     spread table; the Target and Verdict columns are yours. -->

| Criterion | Target | Seed 42 | Seed 7 | Seed 2024 | Verdict |
|---|---|---|---|---|---|
| 1.  |  |  |  |  |  |
| 2.  |  |  |  |  |  |
| 3.  |  |  |  |  |  |
| 4.  |  |  |  |  |  |
| 5.  |  |  |  |  |  |

### Confusion matrix

<!-- ⚠️ TYPED AS A MARKDOWN TABLE. The notebook prints one ready to paste.
     An image of a matrix earns nothing. -->

| true \ predicted |  |  |  |
|---|---|---|---|
| **** |  |  |  |
| **** |  |  |  |
| **** |  |  |  |

**My biggest off-diagonal number, and what it means:**
<!-- Not "the model made mistakes" — WHICH boundary it didn't learn, and which
     direction. "7 real analysis posts were called hot_take and only 3 went the
     other way" is a direction, not just an error rate. -->



---

## Verdicts and Diagnoses

<!-- MET or MISSED against LAST UNIT's target. The target has to hold across
     all three seeds, not turn up sometimes. -->

| # | Criterion | Target | Verdict | How I decided |
|---|---|---|---|---|
| 1 |  |  |  |  |
| 2 |  |  |  |  |
| 3 |  |  |  |  |
| 4 |  |  |  |  |
| 5 |  |  |  |  |

**Diagnoses**

<!-- For each miss: the cause, and how you know. The four common causes are:
     too few examples for a label, a boundary you applied inconsistently, a
     genuinely hard label pair, and a task the model can't reach from this
     much data.

     ⚠️ Use your agreement report as evidence. It is the only instrument you
     have that can tell a LABELLING problem from a MODEL problem, and this
     section is graded on whether you used it that way. -->



---

## Agreement Report

<!-- Your rate against the staff set, and every disagreement adjudicated.

     Remember you labelled these 30 under the STAFF taxonomy in
     data/staff_taxonomy.md, not your own — so every argument below is made
     from those definitions and those decision rules. -->

**Agreement rate:** ___ / 30 = ___%

<!-- Nobody grades this number. A 60% who argues every disagreement from the
     stated rules beats a 95% who wrote "staff was right" nine times. Several
     of the 30 were chosen because they're genuinely ambiguous — you should be
     winning some of these. -->

**Disagreements**

<!-- Three lines each: the post, both labels, and who you think is right and
     why — grounded in the staff definitions you were both applying.

     Then sort each into one of three piles:
       (a) the rule covered it and I applied it loosely → a consistency problem
       (b) the rule genuinely doesn't say               → a gap in the definitions
       (c) the rule is ambiguous here and my reading is defensible → argue it.
           This is a legitimate win.

     Pile (a) is the one that matters most for your diagnosis: if you applied a
     written rule two different ways on 30 posts, that is direct evidence about
     what you did across your own 200. -->

**1.**
> *The post:*
>
> *Staff said / I said:*
>
> *My call, and why:*
>
> *Which pile:*

**2.**
> *The post:*
>
> *Staff said / I said:*
>
> *My call, and why:*
>
> *Which pile:*

**What the pattern in my disagreements tells me:**



---

## The Improvement

**What I changed:**

**Which diagnosis pointed at it:**

### Run Log — After

| Criterion | Target | Seed 42 | Seed 7 | Seed 2024 | Verdict |
|---|---|---|---|---|---|
| 1.  |  |  |  |  |  |
| 2.  |  |  |  |  |  |
| 3.  |  |  |  |  |  |
| 4.  |  |  |  |  |  |
| 5.  |  |  |  |  |  |

**Did it help, and how do I know:**

<!-- If it didn't, say so. Relabelling that didn't help is a genuinely
     interesting result and earns full credit. -->



---

## What's Still Broken

<!-- For each criterion still missed: what you'd do, and why you stopped. -->



**The gap between what I meant my labels to capture and what the model
learned:**
<!-- Two sentences. Your confusion matrix is the evidence. -->



<!-- ═════════════════════════════════════════════════════════════════════

     SUBMISSION CHECKLIST — unit 5

       [ ] criteria.md has five numbered criteria, each naming a NUMBER
       [ ] Each has a reason underneath tied to your data or taxonomy
       [ ] labels.csv: at least 150 rows, text/label/note, ONE file not split
       [ ] No label above 70%
       [ ] All five unit 5 sections have real content
       [ ] Label Taxonomy includes the decision rule for your hardest boundary
       [ ] The Dataset includes three hard cases
       [ ] results.json and test_split.csv committed (the notebook does this)
       [ ] At least four new commits
       [ ] Repository URL submitted — WRITE IT DOWN

     SUBMISSION CHECKLIST — unit 6

       [ ] Baseline vs. Trained table, with your prediction written beforehand
       [ ] Run Log — Before, five criteria across three seeds
       [ ] Confusion matrix TYPED AS A MARKDOWN TABLE
       [ ] A verdict on every criterion
       [ ] A diagnosis for every miss, using the agreement report as evidence
       [ ] Agreement Report with every disagreement adjudicated
       [ ] One improvement, with Run Log — After
       [ ] What's Still Broken
       [ ] results_three_seeds_before.json, results_three_seeds_after.json,
           baseline_results.json and
           agreement_results.json committed
       [ ] At least four new commits
       [ ] The SAME repository URL as last unit

     Do not delete and recreate this repository.
     ═════════════════════════════════════════════════════════════════════ -->

---

📖 **How to run this project: [RUNNING.md](RUNNING.md)**
