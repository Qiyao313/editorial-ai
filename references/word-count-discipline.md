# Word Count Discipline · 字数稳定

> AI models under-deliver on word count by 30-50% by default. Editorial AI fixes this with a three-layer enforcement system.

## The problem

Standard prompts like "write 1,500 words on X" produce output around 800-1,100 words. The model:
- Picks the shortest length that "feels complete"
- Stops at natural rhythm breaks, not at the target
- Treats word count as a soft suggestion, not a contract

The downstream cost: the writer either accepts the short version (defeating the AI productivity gain) or re-prompts the model (doubling the cost and time).

## Three-layer enforcement

### Layer 1: Prompt repetition

The target word count appears in **three places** in the generated prompt:

1. Inside the skeleton block, as a top-level constraint:
   ```
   字数: {wordCount} 字
   ```

2. Inside the hard rules block, framed as a deliverable contract:
   ```
   【硬约束】
   - 字数: {wordCount} 字 (deliverable contract, not soft target)
   - 输出直接给成稿正文, 不要"好的我来帮你..."等开场白
   ```

3. Inside the closing instruction, framed as a checkable spec:
   ```
   Target word count: {wordCount}. Acceptable range: {0.85 * wordCount}-{1.15 * wordCount}.
   Below this range = rewrite triggered.
   ```

### Layer 2: Token budget headroom

```javascript
max_tokens: Math.min(8192, Math.ceil(wordCount * 3))
```

Chinese characters consume ~2-3 tokens per character. Setting `max_tokens` at `wordCount * 3` prevents premature truncation while staying inside provider limits.

For mixed Chinese-English content, this also covers the higher tokenization overhead of code blocks, citations, and English terms embedded in the prose.

### Layer 3: Post-generation length veto

After generation, the reflection scorer measures `actualLength / targetLength`:

- **>= 1.15x** target → acceptable (or check for padding)
- **0.85x to 1.15x** target → ✅ passes length check
- **0.70x to 0.85x** target → ⚠️ flagged but not auto-rewrite
- **< 0.70x** target → 🚨 forced rewrite triggered

When triggered, the rewrite prompt includes:

```
The previous version was {actualLength} characters.
Target: {targetLength} characters.
Under-delivered by {percentShort}%.

Expand by adding SUBSTANCE, not filler:
- Add concrete examples for [thin sections identified by the scorer]
- Deepen analysis on [low-detail areas]
- Add 1-2 specific data points / sources where text was abstract
- Add 1 cross-section connection that the original missed

DO NOT pad with:
- Synonyms or restatements
- "Furthermore" / "In addition" filler transitions
- Generic explanations the reader already understands
```

The scorer re-evaluates the rewritten version on all 7 (or 11) dimensions and keeps whichever scores higher overall.

## Why this works

The mechanism succeeds because:

1. **Repetition defeats the "soft target" default** — when word count appears 3x in the prompt, the model treats it as a contract.

2. **Token headroom prevents involuntary truncation** — many under-delivery cases are actually `max_tokens` exhausted mid-sentence, not the model stopping voluntarily.

3. **Substance-targeted rewriting** beats generic "expand this" prompts — by telling the model exactly which dimensions were thin, the rewrite adds value instead of filler.

4. **Scoring after rewrite** prevents regression — if the rewrite over-pads with filler, the reduced punchline / detail dimensions drop the score below the original, and the original wins.

## Observed results (internal benchmarks)

Across ~200 internal benchmark generations:

- **Without word count discipline**: 47% of generations under-delivered (≥15% short of target)
- **With Layer 1 only** (prompt repetition): 28% under-delivered
- **With Layers 1+2** (prompt + token budget): 12% under-delivered
- **With all three layers** (full enforcement): **3.5% under-delivered**

The remaining 3.5% are typically pure narrative / personal essay pieces where the model legitimately ran out of substance — these are surfaced to the user with a "consider expanding the skeleton" prompt rather than auto-rewriting.

## Edge cases

- **Very short targets** (<300 chars / words): Layer 3 disabled — at small targets, ±15% is just 1-2 sentences and rewrites can over-correct.
- **Very long targets** (>3,000 words): Layer 2 caps at `max_tokens: 8192` (provider limit). For >3,000 word targets, the system generates in two passes (first half + second half) and stitches them.
- **User-specified strict mode**: User can pass `strictLength: true` to make Layer 3 threshold tighter (>= 0.85x instead of >= 0.70x). Useful for press releases / commissioned pieces where length is contractually fixed.
