# Acceptance criteria — TakeMeter

Five criteria that say what "working" means for this classifier, written in
unit 5 **before** anything was trained.

**All five are yours this time.** None are given. You've had two projects of
practice.

An acceptance criterion names a number. *"The model is accurate"* is an
opinion. *"Every label has an F1 of at least 0.60 on the held-out set"* is a
criterion.

Under each, write a sentence or two on **why that number**. A reason that says
something about your data or your taxonomy earns credit — *"I picked 0.60 F1
for `reaction` because it's my smallest label and I only have about 50
examples of it"*. A reason that could be attached to any project does not.

> Missing your own targets next unit costs you nothing. Setting a target so
> easy you can't miss it does.

---

## Pick numbers you can defend

Not numbers that sound impressive. Three labels means a coin-flip guesser gets
about 33%, so a target of 0.40 is barely a target. Your number should sit
somewhere you'd honestly call useful.

**Cover at least three of these five areas.** They're here as prompts, not as a
form to fill in — a criterion that fits none of them is fine if it names a
number.

| Area | A question it could answer |
|---|---|
| Overall accuracy | How often does it need to be right to be worth using? |
| Per-label performance | Is one label allowed to be much worse than the others? |
| Balance | How lopsided can your label counts get before it's a problem? |
| Consistency | If someone else labelled the same posts, how often should you agree? |
| Confidence | Should a confident prediction be right more often than an unsure one? |

Two things worth knowing before you pick numbers, because both will affect
whether you hit them:

- **Your smallest label will have the jumpiest score.** If a label has 50
  examples, about 8 land in the test split. An F1 computed on 8 examples moves
  a lot between seeds. A target for that label should be looser than one for
  your biggest label, and saying so is a good reason.
- **Unit 6 tests across three seeds, and the target has to hold across all
  three.** A target of 0.65 against results of 0.71, 0.62, 0.68 is a **miss**.
  Pick with that in mind — it is stricter than it first sounds.

---

## 1. Overall Accuracy on Unseen Posts

The classifier correctly predicts the label for at least **72%** (at least 22 out of 30) of newly tested posts across multiple test runs.

**Why this target:**
Since there are three categories, a coin-toss or random guess gets only about 33%. Setting the bar at 72% means the model must get nearly three out of four posts right to be considered practically useful for sorting forum threads, which leaves little margin for error on a small test batch.

---

## 2. Minimum Performance for Each Category

The model correctly identifies at least **65%** of posts in each individual category (`analysis`, `hot_take`, and `reaction`), so no category falls behind.

**Why this target:**
`analysis` and `hot_take` are easily confused when runners throw in casual numbers or times as emotional decoration. Requiring every category to stay above 65% prevents the model from ignoring tricky hot takes just to boost its total score, though keeping that high on our smallest slices of test data will be tough to achieve consistently.

---

## 3. Dataset Balance

No single category makes up more than **40%** of our total 200 posts (keeping each label between 60 and 80 examples).

**Why this target:**
If one category dominated (like 60% or 70%), the classifier could get a high score simply by guessing the common label over and over. Enforcing a strict 40% ceiling ensures the training data remains evenly split across all three post types.

---

## 4. Clear Improvement Over the Baseline

The trained classifier beats the un-fine-tuned baseline by at least **15 percentage points** in overall accuracy.

**Why this target:**
Without fine-tuning, a general AI model only guesses from our three written definitions. Because running communities use heavy inside slang ("Zone 2", "super shoes", "Pfitz"), spending the effort to train on 200 custom examples is only worth it if the model shows a substantial 15% jump in real-world performance over just reading the raw definitions.

---

## 5. Reliability on High-Certainty Predictions

When the classifier is highly confident in its answer (at or above **85% certainty**), it must be correct at least **90%** of the time.

**Why this target:**
If this tool were used to automatically flag or organize posts on a forum, users and moderators need to know that when the system says it is sure, it is almost never wrong. Demanding 90% accuracy on high-confidence calls tests whether the model's confidence reflects genuine accuracy rather than overconfident guesses.




---

<!-- ─────────────────────────────────────────────────────────────────────────
     UNIT 6 — read this before you change anything above.

     If a criterion turns out to be BROKEN rather than merely unmet, you can
     revise it, and that earns credit. But never delete or edit the original
     line. Add the revision underneath, like this:

         ## 2. Every label performs acceptably

         The model performs well on all labels.

         **Why this target:** ...

         > **Revised in unit 6:** Every label has an F1 of at least 0.60 on
         > the held-out set.
         >
         > **Why revised:** "performs well" gave me nothing to check. I
         > couldn't produce a verdict from it at all.

     That's a revision because the criterion couldn't be MEASURED.

     Lowering a target because you missed it is not a revision, and it costs
     you the point:

         ✗ "Overall accuracy of at least 0.65" → "at least 0.55", because
            0.65 turned out to be optimistic for 200 examples.

     A number you missed stays where it is, gets diagnosed, and gets a fix
     attempted. That's where the points are.
     ───────────────────────────────────────────────────────────────────────── -->
