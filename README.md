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

TakeMeter sorts comments from **r/nba** — top-level comments (plus some self-contained
replies) from post-game threads of the 2026 NBA playoffs — into three labels: `analysis`,
`hot_take` and `reaction`. The labels are defined by what a post *does*, not its tone: does it
make an arguable claim about basketball, and does it back that claim with a specific on-court
fact? r/nba is loud, profane and full of in-jokes, so a furious comment with a real stat in it
still counts as an argument, and a calm comment with no claim is still just a reaction. The
goal is to pull the posts that actually argue something with evidence out of a thread of
thousands.

---

## Label Taxonomy

Three labels, defined by what a post *does*, not its tone. Same definitions are in
`label_definitions.txt` (read by `baseline.py`). Working notes: `notes/milestone1.md`
(reading + tally) and `notes/milestone2.md` (taxonomy + stress test).

**Why no "joke" label:** in my reading of 80 comments, 45% were jokes, memes, sarcasm or
taunts. A separate joke label would be the fuzziest call in the dataset, because sarcasm
depends on in-jokes. Defining labels by structure lets every joke land somewhere by a
checkable test.

### `analysis`

**Definition:** Makes an arguable claim about basketball and supports it with a specific
on-court fact — a stat, score, minutes, a quote, or a specific described play — offered as the
reason for the claim.

**Example 1:**
> KAT is way to important to this team to get into foul trouble. I'm sorry Mitchell Robinson is unplayable on both ends of the floor. The knicks won KAT's minutes by a fuckton. He needs to play 40 minutes at this rate. He was +17 in a 1 point win

**Example 2:**
> Mitch Johnson just cost Spurs the title. You can't shorten Wembys rest when you are up 17 points. He only rested him 50 seconds in the 2nd half. Spurs played with 4 the whole 2nd half because Wemby gassed out.

### `hot_take`

**Definition:** Makes an arguable claim about basketball (including predictions) but offers no
specific on-court fact in support.

**Example 1:**
> OG deserves the FMVP

**Example 2:**
> Wemby may just become the best player to ever touch a basketball.

### `reaction`

**Definition:** Makes no arguable claim about basketball — a feeling, a joke, a meme, a taunt,
or a restatement of what happened, even if it contains a number.

**Example 1:**
> Amazing game

**Example 2:**
> Bad night to be a car parked in Manhattan

### The hardest boundary

**Which two labels:** `analysis` vs `hot_take`

**The decision rule I used every time:**
A post is `analysis` only if all three hold: (1) it makes an arguable claim; (2) it contains a
specific on-court fact — a number about play, a quote, or a specific described play; (3) that
fact is offered as support for the claim. Then:
- **Tone never changes the label.** An insult with a supporting stat is still `analysis`.
- **Evidence strength is not judged.** If a relevant fact is offered as support, it's
  `analysis` even if the argument is weak ("is a fact offered?" is checkable; "is the evidence
  good?" is opinion).
- **Contract, salary, age, experience or draft position alone are context, not support.**
- **Vague descriptions don't count** ("so many boneheaded moves", "kept chucking 3s") → `hot_take`.

Secondary rule (`hot_take` vs `reaction`): if you could reply *"no, that's wrong"* to it as a
statement about basketball, it has a claim. A bare number with no claim is `reaction`. Sarcasm
is labeled by what it actually means.

*The borderline post this rule was written for:* "Chet fucking stinks, Ozempic Abraham Lincoln
is a no show once again 4 points on 1/2 FG in G7 of the WCF? With B2B Finals on the line?" →
`analysis` (claim: no-show; fact: 4 points on 1/2 FG; insult ignored). Contrast: "De'Aaron Fox
is getting paid close to a quarter of a BILLION dollars to be a basketball terrorist" →
`hot_take` (the only fact is a contract, which is context, not on-court support).

---

## The Dataset

**Where the posts came from:** two r/nba post-game threads, saved as PDFs (comments sorted by
top) and extracted:
- Finals Game 4 — Knicks overcome a 29-point deficit vs the Spurs, June 10 2026 (https://redd.it/1u2ntjw)
- West Finals Game 7 — Spurs eliminate the Thunder, May 31 2026 (https://redd.it/1tsj4wx)

Top-level comments plus replies that are understandable on their own; whole posts kept, line
breaks joined with spaces. **Excluded:** posts with no basketball content (pure political
jokes, a recurring "HITLER DEAD" meme) and posts that only insult a city's residents.

**Selection disclosure:** raw r/nba comments skew heavily toward `reaction` (≈63% in my
reading) and away from `analysis` (≈16%). To meet my balance criterion (no label above 50%),
**62 replies were deliberately selected because they argue something** (`analysis` or
`hot_take`). The dataset is therefore more argumentative than r/nba itself.

**How I labelled them:**
1. **20 posts labeled cold by me** — no notes, no AI — first (`note` = `cold`).
2. **180 posts pre-labeled by Claude** applying my written decision rule (`note` = `prelabeled`).
3. **Review:** I read all 180 pre-labeled rows against my decision rule and changed none.

Finding: **6 of my 20 cold labels don't follow my own written rule** — my instinct used
`analysis` for "talks about a specific game moment" rather than "offers a fact as support."
I kept the written rule for the other 180, and left the cold labels exactly as I made them;
for those 6, the `note` column records what the strict rule would say.

**Counts per label:**

| Label | Count | Share |
|---|---|---|
| `reaction` | 73 | 36.5% |
| `hot_take` | 71 | 35.5% |
| `analysis` | 56 | 28.0% |
| **Total** | 200 | 100% |

**Three hard cases**

**1.**
> *The post:* "Chet fucking stinks, Ozempic Abraham Lincoln is a no show once again 4 points on 1/2 FG in G7 of the WCF? With B2B Finals on the line?"
>
> *Could have been:* `reaction` (it's an insult) or `analysis`
>
> *I chose, because:* `analysis` — the stat line is the evidence for "no show," and my rule ignores tone.

**2.**
> *The post:* "Exactly. Not only playoff experience but just experience in general. Their starting five has an average of just five years of NBA experience, with almost half of that coming from Fox alone. Meanwhile, the Knicks starters have an average of nine years in the league each. That's a huge gap"
>
> *Could have been:* `analysis` (it has real numbers) or `hot_take`
>
> *I chose, because:* `hot_take` — years of experience are context, like age or salary, not a fact about what happened on the court.

**3.**
> *The post:* "Spurs up 27 at the half and then only scoring 30 in the second half is just unspeakable"
>
> *Could have been:* `analysis` or `reaction` (is "unspeakable" an arguable claim or a feeling?)
>
> *I chose, because:* `analysis` — the numbers are offered as the reason for a judgment about the collapse. This is the case I'm least sure another labeler would agree with.

**Known limitation:** all posts come from two games, so the same players (Fox, Wembanyama,
Holmgren, Anunoby) appear everywhere; the model may learn player names as shortcuts.

---

## The Training Run

**Base model:** `distilbert-base-uncased`

**Settings:** 3 epochs, learning rate 2e-5, batch size 16, max length 128 tokens, seed 42.

**Anything I changed from the defaults, and why:** Nothing — all notebook defaults.
Trained on `mps` (Apple Silicon GPU via MPS), torch 2.2.2, as recorded in `results.json`. This environment's Python is an x86_64 build, which limits PyTorch to 2.2.2; that version needs NumPy 1.x, so the environment pins `numpy<2`, `scipy<1.14`, `scikit-learn<1.6` and `pyarrow<18` (exact versions in `requirements-lock.txt`). Hardware and library versions can change results for the same data and seed, so they're part of the result.

**Split sizes:** 140 train / 30 val / 30 test (stratified, seed 42). Test split per label: `hot_take` 11, `reaction` 11, `analysis` 8. `analysis` sits right at the ~8 threshold, so its F1 is likely to move the most between seeds.

---

## How I Used AI

**Moment 1**

- *What I asked for:* After writing my three label definitions, I asked Claude for eight posts designed to sit on the boundaries between them, so I could check whether the definitions held up.
- *What came back:* Eight posts. Two exposed gaps: "Brunson is the best clutch player in the league and it's not close. 36 tonight." showed my rule didn't say whether *weak* evidence counts, and "Imagine paying Chet $240M to take two shots" showed salary was being treated as evidence.
- *What I changed:* I added two clauses to my decision rule — evidence strength is not judged, and contract/salary alone is context, not support.

**Moment 2**

- *What I asked for:* After labeling 20 posts cold, I asked Claude to compare my labels with my own written two-question rule.
- *What came back:* 6 of 20 didn't follow the rule — I had used `analysis` for posts that mention a specific game moment but give no fact as support (e.g. "Fox should have never put that shot, lost the game").
- *What I changed:* I chose to keep the written rule for the remaining 180 posts rather than rewrite it to match my instinct, and left the cold labels untouched as an honest record.

**Pre-labelling disclosure:** 180 of 200 rows in `labels.csv` were pre-labeled by Claude using my written decision rule and are marked `prelabeled` in the `note` column; the 20 `cold` rows are mine alone. Review: I read all 180 pre-labeled rows and changed none. Other AI use: Claude helped debug the environment (NumPy/PyTorch incompatibility, GitHub token auth), extracted comments from my saved thread PDFs, drafted descriptive tags for my Milestone 1 reading notes (which I reviewed), and proposed the structural framing of the labels (I chose it over alternatives).
For `criteria.md`, Claude supplied context (test-split sizes, chance rate, what
`agreement.py` measures) and drafted the wording; every target number was my choice.

<!-- ═══════════════════════ UNIT 6 — THE TEST ═══════════════════════

     Don't fill these in during unit 5.
     ═══════════════════════════════════════════════════════════════════ -->

---

## Baseline vs. Trained

<!-- Both models on the same posts. `python baseline.py --trained results.json`
     prints this table for you. -->

| Measure | Baseline (my definitions) | Baseline (bare label names) | Trained (seed 42) | Trained − best baseline |
|---|---|---|---|---|
| Overall accuracy | 0.333 | 0.433 | 0.667 | +0.233 |
| Macro F1 | 0.167 | 0.329 | 0.667 | +0.338 |
| F1 — `analysis` | 0.000 | 0.000 | 0.667 | +0.667 |
| F1 — `hot_take` | 0.500 | 0.455 | 0.571 | +0.071 |
| F1 — `reaction` | 0.000 | 0.533 | 0.762 | +0.229 |

Baseline: `facebook/bart-large-mnli`, zero-shot, on the same 30 posts in `test_split.csv`.
The official baseline (`baseline_results.json`) is the run with my definitions; the bare-names
run is saved in `notes/baseline_bare_names.txt`.

**What I predicted before I looked:**
The trained model will beat the baseline on all three labels, with the biggest gain on `reaction` — those posts are short and emotional ("HOLY SHIT", "Amazing game"), which should be easy to learn from examples even though the zero-shot baseline scored 0.000 F1 on them. (Written after seeing the baseline, before seeing the trained comparison.)
<!-- Milestone 1 asks you to write this BEFORE seeing the trained numbers. A
     prediction made afterwards isn't one. -->

**What the gap actually means:**
Fine-tuning clearly added something: the trained model beats the *stronger* baseline by 0.233
accuracy. But the comparison has a confound. **My definitions made the zero-shot baseline worse,
not better** (0.333 vs 0.433 with bare names), and the drop is almost all `reaction` (0.533 → 0.000).
My `reaction` definition starts "makes **no** arguable claim," and NLI models are weak at negation,
so a definition written precisely for human labelers turned out to be a poor prompt for this model.
So my prediction was half right: against my-definitions baseline the biggest gain is `reaction`
(+0.762), but against the fairer bare-names baseline the biggest gain is `analysis` (+0.667). No
zero-shot version identified a single `analysis` post — that is where fine-tuning added the most
real value. `hot_take` barely improved (+0.071 over its best baseline), consistent with my
expectation that it's the hardest label. Caveat: this is one seed on 30 posts (one post = 3.3
points); the three-seed run below is the real measurement.
<!-- If the baseline matched your trained model, your fine-tuning added
     nothing — and that is a real finding, not a failure. Say it plainly. -->



---

## Run Log — Before

<!-- Five criteria across three seeds. The notebook's section 6 prints the
     spread table; the Target and Verdict columns are yours. -->

| Criterion | Target | Seed 42 | Seed 7 | Seed 2024 | Verdict |
|---|---|---|---|---|---|
| 1. Overall accuracy | ≥ 0.70 every seed | 0.667 | 0.567 | 0.533 | MISSED |
| 2. `hot_take` F1 | ≥ 0.55 every seed | 0.571 | 0.615 | 0.476 | MISSED |
| 3. No label > 50% of `labels.csv` | ≤ 50% | 36.5% | 36.5% | 36.5% | MET |
| 4. Agreement with staff labels | ≥ 70% | 100% (30/30) | — | — | MET (one check, not seed-dependent) |
| 5. Confidence gap (most-sure − least-sure third) | ≥ 0.20 every seed | 0.10 | 0.50 | 0.30 | MISSED |

Device: `mps` (Apple Silicon GPU), torch 2.2.2 — same machine for all three seeds.
Criterion 3 is a property of `labels.csv` (max share = `reaction`, 73/200), so it's the same on every seed.

Full spread across seeds (from notebook section 6):

| Measure | Seed 42 | Seed 7 | Seed 2024 | Spread |
|---|---|---|---|---|
| Overall accuracy | 0.667 | 0.567 | 0.533 | 0.133 |
| Macro F1 | 0.667 | 0.436 | 0.536 | 0.231 |
| F1 — `analysis` | 0.667 | 0.000 | 0.632 | 0.667 |
| F1 — `hot_take` | 0.571 | 0.615 | 0.476 | 0.139 |
| F1 — `reaction` | 0.762 | 0.692 | 0.500 | 0.262 |

Accuracy moved 13.3 points across seeds — more than the ~10-point line where a measure stops being stable.
Mean predicted confidence was only 0.37–0.39 on every seed (0.33 would be a uniform guess), and eval loss
ended at ~1.00–1.04 vs ~1.10 for chance: the model barely moved from its starting point in 3 epochs.

### Confusion matrix

<!-- ⚠️ TYPED AS A MARKDOWN TABLE. The notebook prints one ready to paste.
     An image of a matrix earns nothing. -->

Seed 42 (one split of 30 posts; diagonal 20/30 = 0.667):

| true \ predicted | analysis | hot_take | reaction |
|---|---|---|---|
| **analysis** | 6 | 2 | 0 |
| **hot_take** | 3 | 6 | 2 |
| **reaction** | 1 | 2 | 8 |

**My biggest off-diagonal number, and what it means:**
3 real `hot_take` posts were called `analysis`, and 2 real `analysis` posts went the other way —
roughly symmetric, so the model hasn't learned where my `analysis`/`hot_take` boundary sits in
either direction. That's the same boundary my decision rule was written for, and the same one my
own cold labels drifted on (3 of my 6 off-rule cold labels were `hot_take`-by-rule posts I called
`analysis`).
<!-- Not "the model made mistakes" — WHICH boundary it didn't learn, and which
     direction. "7 real analysis posts were called hot_take and only 3 went the
     other way" is a direction, not just an error rate. -->



---

## Verdicts and Diagnoses

<!-- MET or MISSED against LAST UNIT's target. The target has to hold across
     all three seeds, not turn up sometimes. -->

| # | Criterion | Target | Verdict | How I decided |
|---|---|---|---|---|
| 1 | Overall accuracy | ≥ 0.70 on every seed | **MISSED** | 0.667 / 0.567 / 0.533 — no seed reached 0.70 |
| 2 | `hot_take` F1 | ≥ 0.55 on every seed | **MISSED** | 0.571 / 0.615 / 0.476 — seed 2024 misses by 0.074; two of three passing isn't "every seed" |
| 3 | No label > 50% of `labels.csv` | ≤ 50% | **MET** | max share is `reaction`, 73/200 = 36.5% |
| 4 | Agreement with staff labels | ≥ 70% | **MET** | 30/30 = 100% (see Agreement Report for why the set may have been easy) |
| 5 | Confidence gap, most-sure − least-sure third | ≥ 0.20 on every seed | **MISSED** | 0.10 / 0.50 / 0.30 — seed 42 is one post short in a third of 10 |

**Diagnoses**

**Criterion 1 (accuracy) — the task isn't reachable with this little training, and the measure is unstable.**
Eval loss finished at ~1.00–1.04 on every seed, against ~1.10 for a uniform guess over three labels; training
loss only fell from ~1.09 to ~1.03 across ~27 gradient steps (139 posts, batch 16, 3 epochs). Mean predicted
confidence was 0.37–0.39, barely above the 0.33 of a uniform guess. The model has hardly moved from its
starting weights, so its top pick often wins by a hair — and which posts land in the 30-post test split swings
accuracy 13 points (0.667 → 0.533). Not a labelling problem: my agreement check shows I apply a written rule
consistently, and the miss is spread across all labels rather than one boundary.

**Criterion 2 (`hot_take` F1) — a genuinely hard pair, made harder by my own rule.**
The matrix confuses `analysis` and `hot_take` in both directions (3 real `hot_take` → `analysis`, 2 the other
way), and the zero-shot baseline couldn't separate this pair either. The agreement report adds the key piece:
staff's Rule 1 calls a decorative number a `hot_take` (sp_014), but my rule says "evidence strength is not
judged," so my training labels put decorated-stat posts in `analysis`. That teaches the model "has a number →
analysis" even when the number does no work, which blurs exactly this boundary. Labelling *inconsistency* is a
smaller factor: the 180 pre-labels follow my rule strictly, and my 6 off-rule cold labels are 6 rows of 200.
Thin data compounds it — 49 `hot_take` and 39 `analysis` training posts per split, and `analysis` F1 hit 0.000
on seed 7.

**Criterion 5 (confidence gap) — an undertrained model gives nearly flat probabilities, on a coarse measure.**
With mean confidence 0.37–0.39, "most-sure" and "least-sure" differ by a few hundredths of probability, so the
ranking into thirds is close to arbitrary. Each third is 10 posts, so seed 42's 0.10 gap is one post. Same root
cause as criterion 1: too little training signal for the model to become confident about anything.

**What the diagnoses point at for the improvement:** criteria 1 and 5 share one cause — the model is barely
trained (loss and confidence near chance). This is the unusual case where the diagnosis points at a training
setting rather than the data.


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

**Agreement rate:** 30 / 30 = 100%

I labelled all 30 under `data/staff_taxonomy.md` and committed `my_staff_labels.csv` before seeing
the staff labels. I applied the four staff rules to each post individually; the file order didn't
drive my calls. Two things still argue for "easy set" over "tight rule," and I'm disclosing both:
- **The staff file is grouped by label** — posts 1–10 are all `analysis`, 11–20 `hot_take`, 21–30
  `reaction` — so the order itself was a possible cue, even though I don't believe it influenced me.
- **Staff said a rate in the 70s is normal**, because several posts were picked to be ambiguous.
  100% on a set built to produce disagreements suggests these posts were cleaner than intended —
  single-topic, no sarcasm, and close to the taxonomy's own worked examples (sp_014 is nearly the
  Rule 1 example verbatim).

**Disagreements:** none, so there's nothing to sort into piles (a)/(b)/(c). In their place: the 7
posts staff flagged as borderline, and the rule that settled each. I matched staff on all 7.

| Post | Competing labels | Rule that settled it | Staff / me |
|---|---|---|---|
| sp_005 "Carbon plates aren't the whole story. My times dropped 2%… and another 2% the following year in the same pair…" | `hot_take` vs `analysis` | Rule 1 — the two comparable numbers carry the argument | `analysis` / `analysis` |
| sp_010 "Three of us ran the same session… within 10 seconds of each other over 5 miles…" | `hot_take` vs `analysis` | Rule 1 — hedged and tiny sample, but the checkable observation is what the claim rests on | `analysis` / `analysis` |
| sp_014 "Times are down about 3% since the shoe rules changed, so obviously the shoes are the whole story…" | `analysis` vs `hot_take` | Rule 1 — the 3% decorates a conclusion already reached ("so obviously") | `hot_take` / `hot_take` |
| sp_019 "Nobody actually enjoys the long run…" | `reaction` vs `hot_take` | Rule 2 — a general claim about everyone, not the writer's own morning | `hot_take` / `hot_take` |
| sp_020 "Every injury thread on this forum is the same injury and the same three replies." | `analysis` vs `hot_take` | Rule 1 — checkable in principle, but no count is given | `hot_take` / `hot_take` |
| sp_028 "Third DNF this year. I don't really want to talk about it…" | `analysis` vs `reaction` | Rule 3 — "third" is countable but nothing is argued from it | `reaction` / `reaction` |
| sp_029 "Just realised the shoes I set every PB in this year are the ones I bought by accident in the wrong size." | `hot_take` vs `reaction` | Rule 2 — the writer's own realisation, now | `reaction` / `reaction` |

**What the pattern in my disagreements tells me:**
1. **I can apply a written rule I didn't write, the way its authors did,** at least on clean,
   single-topic posts — including all 7 they flagged as hard.
2. **It does not show my own 200 r/nba labels are consistent.** Different domain (sarcasm,
   in-jokes, insults) and a different rule. My strongest evidence about my own consistency is
   still that **6 of my 20 cold labels didn't follow my own written rule**.
3. **The most useful finding is a rule difference, not an error.** sp_014 is `hot_take` under staff
   Rule 1 ("the fact must be load-bearing") — but under **my** unit 5 rule ("evidence strength is
   not judged") it would be `analysis`. My training labels deliberately put decorated-stat posts in
   `analysis`, so my model is being taught "contains a number → analysis" even when the number does
   no work. That is a taxonomy choice baked into my data, and it's a candidate cause for my model's
   `analysis`↔`hot_take` confusion.

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