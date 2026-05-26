# Skeleton Template · 骨架模板

> Use this template to construct the prompt for Editorial AI. The user fills the fields; Claude pours the resulting structured spec into the AI as a hard constraint.

## The 5 required fields (all modes)

```
1. Topic (一句话主题)
   What is this piece about, in one sentence?
   Example: "Why Tesla's price cut hurts the dealer network more than it hurts BYD"

2. Hook Angle (钩子角度)
   What's the first sentence going to do? What surprising / contrarian / counter-intuitive entry point?
   Example: "Everyone thinks Tesla's price war is aimed at BYD. The real victim is sitting on dealer lots."

3. Real Contrast (真实反差)
   What specific A vs. B comparison have you confirmed is true?
   Example: "Tesla cut prices 4 times in 2025 (-18% YTD); BYD held prices (-0%); dealer inventory: Tesla +23%, BYD -5%"

4. Real Numbers / Facts (真实数字/细节)
   What specific data points must the AI use literally? List them all. Anything not on this list, AI cannot invent.
   Example: "Tesla Q4 2025 dealer inventory days = 47 (up from 31 in Q1); BYD = 19 (down from 24); industry avg = 35"

5. Core Thesis (核心论点)
   What's the one argument the piece will land on?
   Example: "Price wars in mature EV markets are no longer about winning customers — they're about starving competitors' distribution capital"

6. Ending Path (结尾路径) — pick one:
   - summary: One sentence that crystallizes the thesis
   - action: A specific next step the reader can take
   - suspense: A specific cliffhanger that makes them want to read part 2
```

## The 5 optional fields (B-side modes only)

For Editorial Mode (industry research) and Corporate Mode (brand communications):

```
7. Target Audience (目标客群)
   Who is reading this? Investors? Industry peers? Policymakers? Internal employees?
   Example: "Early-stage VCs covering Chinese EV supply chain"

8. Source Types (信源类型)
   What types of sources does the AI have to attribute to?
   Example: "Tesla 2025 Q4 earnings call + ChinaEV100 industry report + Two anonymous dealer interviews (paraphrased, not quoted)"

9. Stakeholders (利益相关方) — Editorial mode only
   List the parties whose perspectives must appear in the piece.
   Example: "Tesla corporate / BYD corporate / dealer association / EV consumers / industry analysts"

10. Disclosure Note (利益声明) — Corporate mode only, REQUIRED
    The piece must include a clear disclosure of who wrote it and for whom.
    Example: "This is corporate communications from [Brand X] Marketing Team. Third-party data is publicly sourced. Internal data is compliance-reviewed."

11. Reporting Stance (报道立场) — Corporate mode only
    What's the editorial tone? Neutral / positive / crisis response / brand narrative?
    Example: "Neutral analytical tone with strategic narrative — explain decision logic, don't self-promote"
```

## The hard constraint prompt

When Claude calls the AI to generate, the prompt must include this block:

```
【📐 Skeleton — you MUST stay inside this structure】

1. Topic: {topic}

2. Hook Angle: {hookAngle}
   → First sentence must enter from this angle. Don't switch angles.

3. Real Contrast (user-provided, MUST use): {realContrast}
   → The piece must clearly present this contrast. Don't substitute. Don't invent a new contrast.

4. Real Numbers / Facts (user-verified, MUST use literally): {realDetails}
   → These numbers are confirmed true by the user. USE THEM. NEVER ADD NEW NUMBERS.
   → If the piece needs a number not in this list, write `[需补]` (or `[CITATION NEEDED]` for English).
   → Forbidden: any new team counts, account counts, prices, durations, percentages, growth rates.

5. Core Thesis: {coreThesis}
   → The piece must center on this argument. Don't drift.

6. Ending Path: {endingType}
   → If summary: one-sentence crystallization, no cliffhanger, no CTA
   → If action: one concrete next step for the reader, ideally with a time anchor
   → If suspense: a specific cliffhanger that makes them want part 2 (FORBIDDEN: "下次说" / "stay tuned" / generic teasers)

【🎯 B-side optional context — apply if provided】

{if targetAudience}: Target audience: {targetAudience}. Adjust depth of explanation accordingly — don't over-explain to experts, don't under-explain to outsiders.

{if sourceType}: Source types: {sourceType}. Every key claim must attribute to one of these sources by name. Banned: "industry sources say", "according to data", "it has been reported" — too vague.

{if stakeholders}: Stakeholders to represent: {stakeholders}. The piece must show ≥2 perspectives, not single-voice advocacy.

{if disclosureNote}: Disclosure: {disclosureNote}. Insert this disclosure clearly in the piece (typically as an italicized note near the top or bottom).

{if reportingStance}: Reporting stance: {reportingStance}. Calibrate tone accordingly.
```

## Validation rules

Before generating, Claude should validate:

- All 5 required fields are non-empty (push back if any is missing)
- `realDetails` is concrete (numbers, names, dates) — not vague ("some data", "industry trends")
- `endingType` is one of {summary, action, suspense}
- For Corporate mode: `disclosureNote` must be filled (the only required B-side field)

If validation fails, ask the user to clarify before generating. Don't proceed with a half-filled skeleton.
