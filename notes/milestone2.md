# Milestone 2 — Label taxonomy

## Why these labels
In my Milestone 1 reading, 45% of top-level r/nba comments were jokes, memes, sarcasm or taunts. I didn't want a separate "joke" label — sarcasm depends on in-jokes and is the fuzziest call in this community. Instead every label is defined by **structure**: does the post make an arguable claim, and does it back it with a specific on-court fact? Tone, profanity and insults are ignored.

## Labels

### `analysis`
**Definition:** Makes an arguable claim about basketball and supports it with a specific on-court fact — a stat, score, minutes, a quote, or a specific described play — offered as the reason for the claim.

- #16 — "KAT is way to important to this team to get into foul trouble. I'm sorry Mitchell Robinson is unplayable on both ends of the floor. The knicks won KAT's minutes by a fuckton. He needs to play 40 minutes at this rate. He was +17 in a 1 point win"
- #37 — "Mitch Johnson just cost Spurs the title. You can't shorten Wembys rest when you are up 17 points. He only rested him 50 seconds in the 2nd half. Spurs played with 4 the whole 2nd half because Wemby gassed out."

### `hot_take`
**Definition:** Makes an arguable claim about basketball (including predictions) but offers no specific on-court fact in support.

- #8 — "OG deserves the FMVP"
- #55 — "Wemby may just become the best player to ever touch a basketball."

### `reaction`
**Definition:** Makes no arguable claim about basketball — a feeling, a joke, a meme, a taunt, or a restatement of what happened.

- #69 — "Amazing game"
- #24 — "Bad night to be a car parked in Manhattan"

## Decision rules

### Rule 1 — `analysis` vs `hot_take` (the hardest boundary)
A post is `analysis` only if **all three** hold:
1. it makes an arguable claim;
2. it contains a **specific on-court fact**: a number about play (points, FG, +/-, minutes, score, a count of shots/fouls), a quote, or a specific described play;
3. the fact is **offered as support** for the claim (stated as a reason, or placed next to the claim to back it).

Then:
- **Tone never changes the label.** An insult with a supporting stat is still `analysis`.
- **I don't judge whether the evidence is strong enough.** If a relevant fact is offered as support, it's `analysis`, even if the argument is weak. (This keeps two labelers consistent — "is this evidence good?" is a matter of opinion; "is a fact offered?" is checkable.)
- **Contract, salary, age, draft position on their own are context, not support.** They don't describe what happened on the court.
- **Vague descriptions don't count:** "so many boneheaded moves", "kept chucking 3s", "terrible all series" → `hot_take`.

**Borderline post this rule was written for — #41:**
"Chet fucking stinks, Ozempic Abraham Lincoln is a no show once again / 4 points on 1/2 FG in G7 of the WCF? With B2B Finals on the line?"
→ **`analysis`.** Claim: Chet was a no-show. Fact: 4 points on 1/2 FG. The fact is the reason. The insult is ignored.

**Contrast — #6:** "De'Aaron Fox is getting paid close to a quarter of a BILLION dollars to be a basketball terrorist"
→ **`hot_take`.** The only fact is his contract, which is context, not on-court support.

### Rule 2 — `hot_take` vs `reaction`
If you could reply *"no, that's wrong"* to it as a statement about basketball, it has a claim.
- A bare number with no claim attached is `reaction` ("THEY WERE DOWN 29").
- **Sarcasm:** label what the post actually means. If the sarcasm carries a real basketball claim, label it as that claim; if it's only mockery, it's `reaction`.
(#27 "Analytically, the 2026 Knicks are the worst team in NBA history" → mockery of an in-joke, no real claim → `reaction`.)

## Stress test
Eight posts written by Claude to sit on my boundaries, labeled against the rules above.

| # | Post | Label | Why / what it exposed |
|---|---|---|---|
| 1 | "Brunson is the best clutch player in the league and it's not close. 36 tonight." | `analysis` | Exposed a gap: does weak evidence count? First draft was unclear. **Rule 1 now says evidence strength isn't judged.** |
| 2 | "Wemby is a fraud lmao 2/9 in the 4th" | `analysis` | Insult + supporting stat. Confirms tone is ignored. |
| 3 | "I can't believe Fox did that, he's been terrible all series" | `hot_take` | "Terrible all series" is vague, not a specific fact. |
| 4 | "Spurs in 6, book it." | `hot_take` | Prediction = arguable claim, no support. |
| 5 | "Imagine paying Chet $240M to take two shots. Couldn't be me." | `analysis` | Sarcastic, but means "Chet underperformed his contract"; "two shots" is the on-court fact. Exposed that salary alone isn't support — **added the contract/salary clause to Rule 1.** |
| 6 | "OKC's offense dies when SGA sits — they were -12 in his 6 bench minutes." | `analysis` | Clean case. |
| 7 | "Refs gave the Knicks 28 free throws, this series is rigged" | `analysis` | Under Rule 1 a relevant fact is offered, so `analysis` — even though most people would call the argument weak. **Known risk:** a staff labeler may call this `hot_take`. Kept the rule because the alternative ("judge evidence quality") would make every label a judgment call. |
| 8 | "Wow. Just wow. 29 points." | `reaction` | Number with no claim. Added the "bare number" line to Rule 2. |

**Result:** 8/8 classifiable after two rule changes (evidence strength not judged; salary/contract alone is context). #7 is the case most likely to disagree with another labeler.

## Hard cases to watch for during labeling (Milestone 4)
- Sarcastic posts with illustrative numbers, e.g. #21 ("Just one more 3pt attempt bro we are 25 points ahead…") — are the numbers facts or part of the joke?
- Posts where the fact is about one thing and the claim about another.
- Posts that only make sense with an in-joke ("terrorism", "Tony Brothers") — collection rule still undecided.

---

## README-ready section (paste into README → Label Taxonomy)

### Label Taxonomy
Three labels, defined by what a post *does*, not its tone:

- **`analysis`** — an arguable claim about basketball supported by a specific on-court fact (stat, score, minutes, quote, or specific described play) offered as the reason. Examples: "…He was +17 in a 1 point win" (KAT foul trouble); "…He only rested him 50 seconds in the 2nd half…" (Mitch Johnson).

- **`hot_take`** — an arguable claim about basketball, including predictions, with no specific on-court support. Examples: "OG deserves the FMVP"; "Wemby may just become the best player to ever touch a basketball."

- **`reaction`** — no arguable claim: a feeling, joke, meme, taunt, or restatement.
  Examples: "Amazing game"; "Bad night to be a car parked in Manhattan".

**Decision rule (analysis vs hot_take):** `analysis` requires a claim, a specific on-court fact, and that fact offered as support. Tone and insults are ignored; evidence strength is not judged; contract/salary/age/draft position alone are context, not support; vague descriptions ("so many boneheaded moves") don't count. A post that gives no claim at all is `reaction`, even if it contains a number.