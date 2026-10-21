# Acceptance criteria — TakeMeter

Five criteria that say what "working" means for this classifier, written in
unit 5 **before** anything was trained.

Community: r/nba top-level comments. Labels: `analysis`, `hot_take`, `reaction`
(defined by what a post does, not its tone — see README > Label Taxonomy).
All test-set criteria must hold on **every one of the three seeds** in unit 6.

---

## 1. Overall accuracy is at least 0.70 on the held-out test set, on every one of the three seeds.

**Measured by:** `accuracy` in `results.json`, and for each seed in
`results_three_seeds_before.json` in unit 6.

**Why this target:** It's about double a random guesser (≈0.33 with three
labels). In my 80-comment reading of r/nba, 62 of 80 (78%) had no "does two
things at once" flag and were easy to tag, so getting 7 in 10 right should be
reachable. I set it a little below 78% because each test split is only about
30 posts.

---

## 2. `hot_take` has an F1 of at least 0.55 on the held-out test set, on every one of the three seeds.

**Measured by:** `per_label.hot_take.f1` in `results.json`, and per seed in
unit 6.

**Why this target:** I expect `hot_take` to be my weakest label because it's
the middle one. It borders `analysis` (is a specific on-court fact offered as
support?) and `reaction` (is there an arguable claim at all?), so it can be
confused in both directions. With about 10 `hot_take` posts per test split,
each mistake moves F1 by roughly 0.05–0.10, so I set it below my 0.70 overall
target. 0.55 is still well above what guessing would give.

---

## 3. No label is more than 50% of `labels.csv`.

**Measured by:** the label counts printed by the notebook's section 2 check
cell (`df["label"].value_counts()`), divided by the total number of rows.

**Why this target:** In my 80-comment reading, about 63% of posts would be
`reaction` and only about 16% `analysis`, so collecting at random would land
well above 50% `reaction`. A 50% limit is stricter than the brief's 70% cap.
It forces me to deliberately collect `analysis` posts (stat threads, award
debates) so the model sees enough of my scarcest label to learn it, instead of
mostly learning to guess `reaction`.

---

## 4. My labels agree with the staff labels on at least 70% of the shared posts.

**Measured by:** the `agreement` percentage printed by `agreement.py` (saved in
`agreement_results.json`) in unit 6.

**Why this target:** My labels are defined by checkable structure — is there an
arguable claim, and is a specific on-court fact offered as support — not by
tone, so most posts should get the same label from a second person. I'm not
aiming higher because of risks specific to r/nba: sarcasm and in-jokes
("terrorism", "Tony Brothers") a staff labeler may read differently, and my
deliberate choice not to judge evidence strength ("28 free throws → rigged"
counts as `analysis`), which some people would call a `hot_take`. 18 of my 80
reading comments did two things at once, and that's where I expect
disagreements.

> **Revised in unit 6 (reason only — target unchanged at 70%):** The reason above assumed a staff
> member would label *my* r/nba posts with *my* definitions. The actual check had me label 30 staff
> posts (a running forum) under the *staff* taxonomy. The 70% target was still measurable as written
> (result: 100%, MET), but the risks I named — r/nba sarcasm, in-jokes, my "evidence strength isn't
> judged" rule — weren't what this check tested.

---

## 5. The model's most-confident third of test predictions is at least 0.20 more accurate than its least-confident third, on every one of the three seeds.

**Measured by:** `confidence.accuracy_most_confident_third` minus
`confidence.accuracy_least_confident_third` in `results.json`, per seed in
unit 6.

**Why this target:** My hardest posts are the ones that do two things at once —
an insult with a stat (#41), sarcasm wrapped around a real critique (#4) — and
a model that has learned my rule should be least sure about exactly those. Each
third is only about 10 posts, so a 0.10 gap is a single post and
indistinguishable from noise. 0.20 means at least two more correct posts in the
confident third, which is the smallest gap I'd believe means something.

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
