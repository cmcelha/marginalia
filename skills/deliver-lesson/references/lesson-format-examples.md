# Lesson format — worked examples

Two examples showing the same text (Art of War) delivered at different cadences to two different student profiles. Both are real outputs from the system's predecessor; use them as calibration anchors, not templates to copy verbatim.

---

## Example A — Short cadence, first-time reader profile

Profile excerpt: *"First-time reader of Sun Tzu. General intellectual background. Comfortable with the Warring States context but not deep in it. Wants Socratic-style pacing with warm pushback."*

### Generated lesson file

```markdown
# Marginalia — Day 5 of 59 (2026-05-29)

**Unit 5 — Waging War, verses 1-7: The cost of war**

---

## Today's passage

> 1. Sun Tzŭ said: In the operations of war, where there are in the field a thousand swift chariots, as many heavy chariots, and a hundred thousand mail-clad soldiers, with provisions enough to carry them a thousand li, the expenditure at home and at the front, including entertainment of guests, small items such as glue and paint, and sums spent on chariots and armor, will reach the total of a thousand ounces of silver per day. Such is the cost of raising an army of 100,000 men.
>
> [verses 2-7 follow, blockquoted, preserving verse numbers]

## Context

This opens Chapter II and marks a deliberate register change. Chapter I was analytical — how to *think* about war using the five factors and seven considerations. Chapter II is economic — what war *costs*, in silver per day and in the slow attrition of weapons, ardor, treasure, and finally the patience of rival chieftains who'd been waiting their turn. Notice that Sun Tzŭ doesn't just say "war is expensive" — he quantifies (a thousand ounces of silver per day, glue and paint included), which is itself a calculation move. As you read, watch verse 5 closely: he criticizes *both* "stupid haste" *and* "cleverness associated with long delays."

## Questions

1. Walk through the chain of consequence in verses 2-4. What does Sun Tzŭ say a protracted campaign does to the army, then to the state, then to the state's standing among other powers? Why does he end the chain with "no man, however wise, will be able to avert the consequences"?

2. Verse 7 says only one acquainted with the *evils* of war can understand its *profitable* conduct. In Chapter I you learned to forecast victory from the seven considerations and to push the enemy into "arrogance" (the state of not calculating). How does the cost frame in Chapter II change what counts as a good Chapter-I calculation — is forecasting that you'll win enough?

---

*Answer in chat when you have a few minutes.*
```

**Why this works at short cadence for this profile:**
- Single short unit (~3-5 min of reading)
- Context is ONE paragraph, anchored in structural observation (register change from analytical to economic), not in historical detail the user doesn't have
- Question 1 is comprehension (walk through the chain). Question 2 is application (does the cost frame change what counts as a good calculation).
- No spoilers — the questions reach toward verse 5's tension without resolving it

---

## Example B — Medium cadence, advanced re-reader profile

Profile excerpt: *"Re-reader. Philosophy: Heidegger, Jung, Lao Tzu, Nietzsche, Greek philosophers. History: Chinese Xia through Qing, Silk Road, Pax Mongolica. Skip basic framing. Question modes: comprehension, prediction, application. Calibration: moderate-to-sharp; push harder than introductory."*

### Generated lesson file (covers units 5, 6, 7 — Chapter II grouped)

```markdown
# Marginalia — Days 5-7 of 59 grouped (2026-05-29)

**Waging War, verses 1-23 (Chapter II): The cost frame**

---

## Today's passage

> [Full Chapter II — units 5, 6, 7 concatenated as blockquoted verses with original numbering preserved]

## Context

Chapter II's deepest move isn't the quantification — it's the *audit*. Chapter I ranks generals by how many calculations they make beforehand (v.26: many → win, few → lose, none → catastrophe); Chapter II adds that the calculation must include cost, not just feasibility. The Warring-States register is doing real work here: Sun Tzŭ writes against an older Spring-and-Autumn doctrine where prolonged ritual campaigns were prestige, not loss. His forage-on-the-enemy move (vv. 8-14) is the operational answer to the audit problem — *redirect* the cost rather than absorb it — and it anticipates the Chapter III "take whole, intact" doctrine that defines his strategic ideal. Watch how vv. 21-23 sit oddly with vv. 8-14: the chapter quietly licenses brutality on enemy stores while denying it on home people, a moral asymmetry the text doesn't resolve.

## Questions

1. The chapter pivots three times — analytic cost frame (vv. 1-7), operational redirection via forage (vv. 8-14), and rewards/penalties for captured material (vv. 15-23). What relationship between *cost* and *agency* is being constructed across the three pivots? Does the general control the cost equation or merely manage it?

2. Sun Tzŭ explicitly refuses prolonged war (v.6: "no instance of a country having benefited from prolonged warfare"). Yet history shows attrition warfare often working (Warring States internal: Qin's eventual consolidation; later: Sino-European examples you know). Predict: when you reach Chapter III, will Sun Tzŭ's preference for swift victory hold as an absolute, or will he carve out an exception for asymmetric cases?

---

*Answer in chat when you have a few minutes.*
```

**Why this works at medium cadence for this profile:**
- Three short units combined into one medium-tier lesson (~12-15 min of reading)
- Context paragraph leans on Warring-States history specifics (Spring-and-Autumn contrast, prestige campaigns) — the profile licenses this depth
- Surfaces a *structural* observation (three pivots in the chapter) the first-time reader wouldn't be ready for
- Question 1 is comprehension at a sharp level (find the structure, then theorize). Question 2 is prediction with historical scaffolding the profile invites.
- Does NOT explain the historical references — the profile signals the user has them already

---

## Calibration notes

When generating a lesson, the choice points are:

| Choice | Driven by |
|---|---|
| How many units in one delivery | `cadence` in progress.json |
| Depth of context paragraph | Profile's `Background` section |
| Which references to anchor in | Profile's listed philosophical/historical knowledge |
| Question modes | Profile's `Question style preferences` |
| Question sharpness | Profile's `Calibration` line |
| Whether to call back | Recent entries in `student_notes.md` Lesson log |

If the profile is sparse (a user who skipped onboarding details), default to the Example A register — first-time reader, comprehension + light application, no assumed prior knowledge.
