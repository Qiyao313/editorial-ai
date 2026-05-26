# Reflection Rubric · 自评 7 / 11 维评分

> After generating, Claude runs this rubric on the draft. Score 60+ passes; <60 triggers an auto-rewrite focused on weak dimensions.

## Core scoring philosophy

Two principles distinguish this rubric from typical LLM-as-judge frameworks:

1. **`needsSolution` is auto-detected, not user-configured.** The judge decides per-piece whether this is the kind of article that needs an actionable prescription (how-to / advisory / analysis) vs. one where prescriptions would ruin it (pure narrative / personal essay / poetry).

2. **Single-dimension hard veto.** Even if a piece scores 9/10 on six dimensions but 2/10 on solution (for a piece where `needsSolution=true`), it **fails** and triggers a forced rewrite. "All sizzle, no steak" is the most damaging AI failure mode — we treat it as a single-point failure.

## The 7 base dimensions (all modes)

Each scored 0-10. Threshold for "good" is 7+.

### 1. Hook (钩子)
Does the first sentence have information / contrast / counter-intuition / specific detail?

- **9-10**: Reader is hooked instantly. Has number/time/contrarian framing.
- **5-7**: Acceptable but missing edge.
- **0-4**: Generic opener ("Today let's talk about..." / "Many people don't realize that...")

### 2. Punchline (金句)
Is there at least 1 sentence in the piece that's screenshot-worthy? Short, sharp, retains meaning after stripping modifiers.

- **9-10**: ≥1 clear punchline.
- **5-7**: A near-punchline weakened by hedging ("perhaps", "at least", "if we consider").
- **0-4**: No punchline.

### 3. Contrast (反差)
Is there a specific comparison creating a memory anchor? (number contrast / time contrast / expectation contrast)

- **9-10**: Clear A vs. B contrast with concrete data.
- **0-4**: Flat narrative without comparison.

### 4. Detail (细节)
Are there ≥3 specific numbers / times / actions? Avoid vague modifiers ("many", "some", "several").

- **9-10**: ≥3 concrete details AND they appear genuine (not fabricated — see `anti-fabrication-rules.md`)
- **5-7**: Some details but with suspicious numbers
- **0-4**: Vague throughout

**Placeholder handling (important)**:
- If user's `realDetails` skeleton field was explicitly empty / all `[需补]`, the AI is **expected** to use placeholders. In this case, detail dimension should be scored on the AI's adherence to the placeholder discipline, not on absent concrete data:
  - All placeholders in the right spots + 0 fabrication → detail = **7** (honest, but waiting on user)
  - Some placeholders + some user-provided data correctly reused → detail = **8-9**
  - Placeholders missing where they should be (AI freelanced numbers) → detail = **3-5** (fabrication, see rules)
- This way, an honest placeholder-heavy draft can still pass; the scorer is rewarding the AI for doing what was asked, while signaling to the user "your skeleton input needs more concrete data" via the `placeholder count >= 8` annotation in `anti-fabrication-rules.md`.

### 5. Rhythm (节奏)
Is the piece structured "hook → contrast → cause → suspense", or is it just sequential ("what is → why → how") flow?

- **9-10**: Clear non-linear structure
- **0-4**: Stream-of-consciousness or "what / why / how" boilerplate

### 6. Ending (收尾)
Specific cliffhanger or value-crystallization? Or generic "we'll talk next time"?

- **9-10**: Specific cliffhanger ("but here's the thing I discovered next that changed everything")
- **0-4**: Boilerplate ("see you next time", "looking forward to your comments")

### 7. Solution (方案 / actionability)
Does this piece give the reader something concrete they can do tonight / this week / this month?

**Step 1: Determine `needsSolution`**

Auto-detect by topic + audience + framing:

| Indicator | needsSolution |
|---|---|
| how-to / tutorial / advisory for individual reader | **true** |
| pro-con analysis with individual reader as decision-maker | **true** |
| consumer guidance / personal action guidance | **true** |
| personal narrative / emotional essay / poetry / nostalgia | **false** |
| **observational industry analysis** (audience = investors / analysts / observers, framing = "what's happening in the market") | **false** — the "prescription" is the market judgment itself, not an action list |
| **policy / regulatory commentary** (audience = policy researchers, framing = "what this means structurally") | **false** — same reason |
| **macro trend report** (audience = strategists, framing = "where is X going") | **false** — readers want forecasts, not chores |

**Disambiguation rule**: if the audience is a professional observer (investor / journalist / analyst / policymaker) and the framing is descriptive ("3 signals X is happening"), `needsSolution = false`. If the audience is an individual decision-maker and the framing is advisory ("how you should X"), `needsSolution = true`.

Editorial mode pieces lean `false` more often than KOL mode pieces — that's by design. Industry observers want analysis, not chores.

**Step 2: Score solution given needsSolution**

When `needsSolution = true`:
- **9-10**: ≥3 concrete actions with time anchor (tonight/this week) + quantity + tool/step
  - ✅ Example: "Tonight, sign a no-AI list with your kid (post it on the desk); This week, audit the AI chat history 10 mins; If overstep → revoke AI access 1-2 days"
- **6-8**: ≥1 concrete action + some general advice
- **3-5**: Only abstract advice ("be careful" / "balance the use" / "stay vigilant") with no operable steps
- **0-2**: Pure diagnosis. The piece describes the problem in depth but proposes no concrete actions the reader can take.

When `needsSolution = false`:
- Score solution honestly (low if no action), but **the veto does not apply**. A poetic essay can pass with solution=2.

### Hard veto rule

If `needsSolution = true` AND `solution < 4`:
- `forcedRewriteForMissingSolution = true`
- `pass = false` regardless of total score
- Trigger forced rewrite targeting the solution dimension

This is the **only** single-dimension veto in the system.

## The 4 additional dimensions (B-side modes only)

For Editorial Mode (industry research) and Corporate Mode (brand communications), add 4 more dimensions:

### 8. Source Transparency (信源透明度)
Does each key claim/data point cite a specific source?

- **9-10**: Every key claim cites specific source (company / report / year / person). E.g., "according to ChinaEV100 2025 Q4 report"
- **6-8**: Most key claims cite sources; occasional "industry sources say"
- **3-5**: Half-or-more claims uncited; multiple "according to reports" / "data shows" vagueness
- **0-2**: Entirely "industry sources" / "research shows" with no specific attribution

**Placeholder handling**: same logic as `detail` — if `realDetails` was empty / all `[需补]`, score on whether the AI **correctly labeled where sources should go** (`[需补]: source name`) rather than freelancing fake citations. Honest placeholder labeling → score 7-8 (not penalized for missing concrete source names that weren't in the skeleton).

### 9. Multi-Side Balance (多方平衡)
For contested topics, are ≥2 perspectives represented?

- **9-10**: Clear ≥2-side representation (e.g., company perspective + analyst perspective + dealer perspective)
- **6-8**: Second voice exists but heavily weighted to one side
- **3-5**: Single perspective + token "some also believe..."
- **0-2**: Single perspective throughout — feels like advocacy / PR piece

**Special**: Pure-fact / technical-explanation pieces (not contested) can score 7-8 without multi-side; don't penalize for lack of false balance.

### 10. Expertise (专业度)
Does the piece show ≥2 "industry-insider only" insights?

- **9-10**: ≥2 insider connections (e.g., "this is connected to last year's policy lag effect" / specific business-model nuance)
- **6-8**: Uses specific terminology + 1 unique insight
- **3-5**: Encyclopedic regurgitation; nothing only an insider would notice
- **0-2**: Anyone could have written this — Wikipedia-level

### 11. Objectivity (客观性)
Does the piece separate "facts" from "opinions"?

- **9-10**: Strict separation. Fact paragraphs use neutral language. Opinion paragraphs explicitly mark "analyst Wang argues..."
- **6-8**: Mostly separated, occasional blur
- **3-5**: Author opinions presented as facts; uses words like "shocking" / "epic" / "myth busted"
- **0-2**: Pure KOL marketing tone — no fact/opinion boundary at all

**Corporate-mode extra**: If a piece pretends to be third-party reporting ("our investigation shows...") when it's actually corporate self-publishing, objectivity drops to ≤4 regardless of other content quality.

## Score aggregation

```
total = sum of all dimensions (auto-skips undefined B-side dimensions in KOL mode)
maxTotal = number of scored dimensions * 10
percentScore = round((total / maxTotal) * 100)

pass = percentScore >= 60
       AND topicAlignment.score >= 60
       AND NOT forcedRewriteForMissingSolution
```

Examples:
- KOL mode: total / 70 → percent (e.g., 56/70 = 80%)
- Editorial / Corporate mode: total / 110 → percent (e.g., 82/110 = 75%)

## Forced rewrite logic

When `pass = false`:

1. Identify weak dimensions (those scoring ≤4)
2. Build a rewrite prompt that includes:
   - The original draft
   - The list of weak dimensions
   - Specific fix hints per dimension (see `DIM_FIX_HINT` mapping below)
3. Generate rewritten version
4. Re-score
5. Keep the higher-scoring version (don't blindly accept the rewrite — it may be worse)

### Dimension fix hints (DIM_FIX_HINT)

| Dimension | Fix hint |
|---|---|
| hook | Open with information/contrast/counter-intuition; ban "Today let's talk about..." |
| punchline | Add ≥1 screenshot-worthy sentence: short, sharp, no hedging |
| contrast | Add 1 specific comparison (before/after / number contrast / time contrast) |
| detail | Add ≥3 specific numbers/times/actions; ban "many / some / a few" |
| rhythm | Restructure "hook → contrast → cause → suspense"; not sequential |
| ending | End with specific cliffhanger; ban "see you next time" |
| solution | Add a "concrete actions" paragraph: ≥3 actions with time anchor + quantity + tool/step. Ban abstract advice ("be careful", "balance it") |
| sourceTransparency | Add specific source for every key claim (company/report/year); kill "industry sources say" |
| multiSide | Add ≥1 contrasting viewpoint; don't write single-voice advocacy |
| expertise | Add ≥2 insider observations the layperson wouldn't catch |
| objectivity | Separate fact paragraphs (neutral) from opinion paragraphs (explicitly attribute). Kill emotion-words ("shocking", "epic"). |

## Output format

The reflection result Claude returns should look like:

```json
{
  "scores": {
    "hook": 9, "punchline": 8, "contrast": 9, "detail": 8,
    "rhythm": 8, "ending": 7, "solution": 9,
    "sourceTransparency": 9, "multiSide": 6, "expertise": 8, "objectivity": 9
  },
  "topicAlignment": {
    "score": 100,
    "verdict": "aligned",
    "reason": "completely on-topic; contains placeholders, fill before publish"
  },
  "needsSolution": true,
  "total": 90,
  "maxTotal": 110,
  "percentScore": 82,
  "pass": true,
  "forcedRewriteForMissingSolution": false
}
```

Then Claude tells the user:

> "Editorial AI scored your draft **82/100** (passed).
> Strong: source attribution (9), hook (9), contrast (9), objectivity (9).
> Weak: multi-side balance (6) — consider adding a counter-perspective from competitors / analysts.
> Found 3 `[需补]` placeholders — fill with real data before publishing."
