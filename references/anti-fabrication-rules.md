# Anti-Fabrication Rules · 反编数字规则

> The single most damaging AI writing failure is plausible-sounding fake data. Editorial AI catches this with a 5-pattern detector and a placeholder system.

## The core rule

**The AI may use only numbers / facts that appear in `realDetails` (skeleton field 4) or that are universally public facts (geography, dates of public events, etc.). Everything else must be a placeholder.**

Placeholder syntax:
- Chinese context: `[需补]`
- English context: `[CITATION NEEDED]`

## The 5 fabrication patterns the scorer catches

### Pattern 1: `[需补]` placeholders themselves — NOT fabrication

⚠️ **Critical rule**: When the AI honestly writes `[需补]`, this is the **opposite** of fabrication. The placeholder is the AI's way of saying "I need real data here but don't have it."

The scorer must treat `[需补]` as a **blue "fill before publish" note**, never as a red "fabrication" warning. The `topicAlignment.reason` field should say "contains placeholders, fill with real data" — NOT "fabricated numbers".

**This breaks the AI's lazy default of inventing plausible numbers.** Once the AI learns honesty is rewarded and invention is punished, it will reliably reach for `[需补]` instead of making something up.

### Pattern 2: Counts of entities (team / account / user / unit)

Examples of the pattern (any of these unsupported = fabrication):
- "2100+ teams are using it"
- "27 sub-vertical industry rules"
- "21 underlying logic pitfalls"
- "17 programmer accounts"
- "the top 100 channels"

**Detection rule**: If the user didn't list the specific count in `realDetails`, AND it's not a publicly verifiable number (e.g., "S&P 500 components = 500" is fine), it's fabrication.

**Scorer penalty**: detail dimension -4

### Pattern 3: Money / pricing / duration

Examples:
- "purchased for $2,300"
- "sold 20,000 copies at $99 each"
- "monthly income of $12,700"
- "1987 yuan for the membership"
- "$299 per year for the course"
- "broke out in acne for 28 days"
- "posted weekly for 3 months"

**Detection rule**: 4-digit-or-smaller currency amounts and time durations without context are fabrication-flagged. "His monthly salary is $5,847" (oddly precise) without a cited source = fabrication.

**Scorer penalty**: detail dimension -3

### Pattern 4: Percentages / statistics

Examples:
- "80% market share"
- "99% of people don't know"
- "completion rate under 1/3"
- "80% of content isn't original"

**Detection rule**: Any percentage without cited source = fabrication. Percentages are the easiest pattern for AI to fake convincingly, so they're treated with strict suspicion.

**Scorer penalty**: detail dimension -3

### Pattern 5: Internal inconsistency

Examples:
- Paragraph 2 says "100 students enrolled"; paragraph 4 says "the cohort of 150 students"
- "The course launched in 2022" then later "by Q3 2021 it had..."

**Detection rule**: Cross-paragraph factual contradictions are fabrication evidence — the AI is freelancing, not tracking facts.

**Scorer penalty**: detail dimension -4

## The detection procedure (scorer prompt)

The reflection AI does this:

1. Take the user's skeleton fields (especially `realDetails` + `realContrast`) as the **fact whitelist**
2. Extract all specific numbers / quantities / prices / percentages / durations from the article
3. For each extracted number, check: is it in the whitelist? Is it `[需补]`?
4. Count violations:
   - 0 violations + any placeholders → detail score 7-9 (good honesty)
   - 1-2 violations → detail score 5-6 (caught some fabrication)
   - 3+ violations → detail score 0-4 (massive fabrication)

## The scoring UI

Front-end shows:
- **Blue chip 🛠️ "contains placeholders, fill before publish"** — when reason includes "placeholder" / "to fill"
- **Red chip 🚨 "AI fabrication suspected"** — when reason includes "fabricated" / "invented" / "false data"

These are mutually exclusive. The blue chip is the "honest gap" signal. The red chip is the "AI made stuff up" signal. Users learn to trust placeholders and audit fabrications.

## Good vs. bad examples

✅ **Good (no penalty)**:
> User says: "we shipped 17 modules over 6 months"
> AI writes: "Over 6 months, the team shipped 17 modules"
> Reuses literally. Detail score: 8.

✅ **Good (placeholder, no penalty)**:
> User says: "we made several writing tools"
> AI writes: "Over the past `[需补]` months, the team shipped `[需补]` writing tools"
> Honestly flags gaps. Detail score: 6-7 (no penalty for placeholders; some penalty for sparse details).

❌ **Bad (fabrication)**:
> User says: "we made several writing tools over a few months"
> AI writes: "Over 6 months, the team shipped 17 modules, used by 2100+ teams"
> Invented "6 months", "17 modules", "2100 teams". Detail score: ≤4. Banner shows red "AI fabrication suspected".

## Why this works

The mechanism succeeds because:

1. **Placeholders are easier than invention** for the AI — once the AI knows `[需补]` is the safe default, it stops reaching for plausible-sounding fake numbers.
2. **The reward asymmetry teaches the model** — placeholder = no penalty, invention = -3 to -5. The AI learns quickly.
3. **The user-facing distinction is visual** — blue chip = honesty, red chip = danger. Users build trust in the system instead of fighting it.

## Placeholder density guidance

Editorial AI does **not** cap the number of placeholders allowed in a draft. A piece can be all-`[需补]` and still pass scoring — placeholders are honest gaps, not failures.

However, the reflection scorer surfaces density information so the user knows what they're dealing with:

| Placeholder count | `topicAlignment.reason` annotation | UI treatment |
|---|---|---|
| 0 | (no annotation) | Green / clean |
| 1-3 | "contains placeholders, fill before publish" | Blue 🛠️ chip |
| 4-7 | "contains several placeholders (N), substantial fact-fill needed" | Blue 🛠️ chip + count |
| 8+ | "placeholder-heavy draft (N), consider expanding skeleton's realDetails field" | Amber ⚠️ chip — the skeleton itself may be thin |

When count ≥ 8, the issue is usually that the user's `realDetails` field was very sparse. The scorer's job is to flag this back to the user as "your skeleton input needs more concrete data", not to penalize the AI for honesty.
