---
name: editorial-ai
description: Use this skill whenever the user asks Claude to write long-form content (articles, reports, business writing, industry analysis, corporate communications, op-eds, white papers, deep takes) and needs the output to be source-grounded, fact-controlled, and structurally disciplined — not free-wheeling AI prose. Triggers on requests like "write me a report on...", "draft an article about...", "give me an analysis of...", "I need a piece on...". Especially trigger when the user mentions journalism, editorial standards, industry research, corporate PR, B2B content, white papers, or expresses frustration about AI making up numbers, AI cliches, or AI writing without giving actionable advice. Trigger regardless of length for serious content (crisis statements, press releases, even short op-eds). Do NOT trigger for casual chats, creative fiction, poetry, pure personal essays, or short marketing copy where AI fluency is the goal (Xiaohongshu种草, ad headlines, Douyin captions).
user_invocable: true
version: "1.0.0"
---

# Editorial AI · 直笔

> AI shouldn't be a careless ghostwriter. It should be an editor with standards.
> AI 不应该是无脑写手, 应该是有底线的编辑。

This skill teaches Claude to write long-form content the way a professional editor would — **structure before words, sources before claims, prescriptions before farewells**. It is built for serious content workers (journalists, analysts, PR teams, B2B writers, researchers) who are tired of AI generating fluent-but-fabricated, slick-but-aimless prose.

## When to use this skill

Trigger this skill when the user wants Claude to write **any of**:

- An industry analysis, market research, or sector report
- A journalism-style article, op-ed, or deep-dive
- A corporate communications piece (founder interview, strategy update, crisis response, brand story)
- A B2B / thought leadership article
- A white paper, position paper, or policy commentary
- Any piece where **factual accuracy and source attribution matter**, regardless of length (a 200-word crisis statement matters just as much as a 2,000-word white paper)

Do **not** trigger this skill for:
- Casual chat, brainstorming, summarization, or Q&A
- Creative fiction, poetry, song lyrics, or pure personal essays (including nostalgia / memoir)
- Short marketing copy **where AI fluency is the goal**: Xiaohongshu 种草, ad headlines, Douyin captions, viral social copy
- Code generation, technical documentation, or data analysis

**The key test is not length — it's purpose.** A 250-word press release about a data breach triggers (factual accuracy is critical). A 1,500-word emotional essay about a deceased grandparent does not trigger (prescription/fact-control would ruin the genre).

## The four disciplines

Editorial AI enforces four disciplines that distinguish professional writing from AI slop:

### 1. 🦴 Skeleton First (骨架先行)

Before generating anything, ask the user to fill **5-7 structural fields** that define the article's spine:

| Field | Required | What it does |
|---|---|---|
| Topic | ✅ | One-sentence subject |
| Hook angle | ✅ | The contrarian / surprising entry point |
| Real contrast | ✅ | A specific comparison user has confirmed is true |
| Real numbers / facts | ✅ | User-verified data the AI must use literally |
| Core thesis | ✅ | The argument the article will land on |
| Ending path | ✅ | summary / action / suspense |
| Target audience | optional | For B-side writing (investors / peers / clients) |
| Source types | optional | For B-side writing (primary interview / public report / internal data) |
| Stakeholders | optional | For multi-side balance (industry research mode) |
| Disclosure note | optional | Required for corporate communications (paid / unpaid / sponsored) |
| Reporting stance | optional | For corporate (neutral / positive / crisis / brand-narrative) |

The AI **must** stay inside this skeleton. It cannot change the hook angle, swap the contrast, or invent a different thesis. It only fills in the flesh (transitions, examples, emotional anchors).

See `references/skeleton-template.md` for the full schema and prompt template.

### 2. 🛠️ Sources Bound, Numbers Locked (信源绑定, 数字封锁)

The AI is **forbidden from inventing any specific number** the user did not provide. This includes:
- Team sizes, user counts, account counts
- Prices, revenues, salaries, time spans
- Percentages, market shares, growth rates
- Reader counts, student counts, follower counts

If the article needs a number that's not in the skeleton, the AI must write `[需补]` (Chinese: "needs fill") or `[CITATION NEEDED]` (English) as a placeholder. **Never invent**.

The reflection scorer treats placeholders as **honest gaps**, not as fabrications — they get a calm blue "fill before publish" tag, never a red "fabrication" alert. This is critical: it tells the AI that **honesty is rewarded, invention is punished**, breaking the lazy default of plausible-sounding fake data.

See `references/anti-fabrication-rules.md` for the 5 fabrication patterns the scorer catches.

### 3. 💊 Diagnosis + Prescription (诊断 + 处方)

Many AI-written articles diagnose a problem brilliantly, cite real data, end with a sharp closing line — but **fail to deliver the actionable solution the ending should provide**. They are diagnoses without prescriptions.

This skill enforces a separate "actionability" dimension in the reflection scorer:

- **For analysis / how-to / advisory pieces**: the article must include ≥3 concrete actions the reader can take *tonight / this week / this month*. Each action must have a time anchor + quantity + tool / step. Pure summarizing won't pass.
- **For personal essays / pure narrative / poetry / nostalgia pieces**: the scorer auto-detects this and does **not** force a prescription — it would ruin the genre.

A piece that scores 9/10 on hook, punchline, contrast, detail, rhythm, and ending but only 2/10 on solution **fails** and triggers a forced rewrite. This is the only single-dimension veto in the system — because "all sizzle, no steak" is the single most common AI writing failure mode.

See `references/reflection-rubric.md` for the full 7-dimension (KOL mode) and 11-dimension (editorial mode) rubrics.

### 4. 📏 Word Count Discipline (字数稳定)

AI models systematically under-deliver on word count. Ask for 1,500 words, get 900. Ask for 2,000, get 1,200. The model picks the lowest acceptable length and stops. For most use cases this means the writer manually pads or re-prompts, defeating the productivity gain.

Editorial AI enforces word count at three layers:

1. **Hard prompt constraint** — `wordCount` is repeated multiple times in the prompt (skeleton block + hard rules block + closing instruction), framed as a deliverable contract, not a soft target.

2. **Token budget headroom** — `max_tokens` is set to `wordCount * 3` (Chinese chars consume more tokens), preventing premature truncation.

3. **Post-generation length veto** — if the output is below 70% of the target word count, the reflection rubric flags `length_short` and triggers a forced rewrite with explicit instructions: "Last version: X chars. Target: Y chars. You under-delivered by Z%. Expand by adding [specific guidance based on what dimensions scored low], not by padding with filler."

The rewrite prompt is calibrated to expand on **substance** (more examples, deeper analysis, more specific data) rather than **filler** (synonyms, restatements). The scorer re-evaluates after rewrite and keeps whichever version scores higher.

See `references/word-count-discipline.md` for the full enforcement details.

## Three writing modes

Editorial AI ships with three pre-configured modes. The user (or Claude reading the request context) picks one:

### 🎯 KOL Mode (新媒体创作 · 流量稿件)
For social media creators, marketing teams, viral content. 7 scoring dimensions: hook / punchline / contrast / detail / rhythm / ending / solution.

### 📰 Editorial Mode (机构媒体 · 行业研究)
For industry analysts, journalists, research institutions. 11 dimensions = KOL 7 + sourceTransparency / multiSide / expertise / objectivity. AI gets a "reporter / editor" perspective prompt.

### 🏢 Corporate Mode (企业传播 · 品牌内容)
For corporate communications, brand teams, PR. 11 dimensions, plus a hard rule: every piece must include a disclosure note (e.g., "This is corporate communications from X company"). AI writes in first-person organizational voice.

See `references/mode-prompts.md` for each mode's full prompt addendum.

## Workflow

When triggered, Claude should:

1. **Identify the mode** — Ask the user, or infer from context (industry report → editorial; founder interview → corporate; viral hook → KOL).

2. **Collect the skeleton** — Walk the user through the 5-7 required fields. Don't accept vague answers — push back: "What specifically is the real contrast? Two data points side by side." If the user has no real numbers, accept it but note that numbers in the output will be `[需补]` placeholders.

3. **Generate within constraints** — Build the prompt using `references/skeleton-template.md` + the chosen mode's prompt from `references/mode-prompts.md` + the global rules from `references/anti-fabrication-rules.md`.

4. **Self-score** — Run the reflection rubric from `references/reflection-rubric.md`. Output the score breakdown (e.g., "Hook 9, Punchline 8, Contrast 9, Detail 7, Rhythm 8, Ending 8, Solution 9 — total 82/100, passed").

5. **Auto-rewrite if needed** — If percentScore < 60, OR if `needsSolution = true` AND `solution < 4`, OR if actual length < 70% of target, automatically rewrite once.
   - The rewrite prompt explicitly names the weak dimensions and gives substance-targeted fix hints (see `references/reflection-rubric.md` DIM_FIX_HINT table — e.g., low `sourceTransparency` → "add specific source for every key claim"; low `solution` → "add 3 concrete actions with time anchor").
   - The rewrite is **substance-targeted**, not generic "make it better". Rewrite expands what's thin, doesn't pad with filler.
   - Re-score the rewritten version and **keep whichever version scores higher** — if the rewrite over-corrects and drops other dimensions, the original wins.

6. **Surface placeholders** — At the end, tell the user "Found N `[需补]` placeholders in the draft — fill with real data before publishing."

## Anti-patterns to refuse

If the user asks Claude to:
- "Write me an article that looks deep but I'll edit the numbers later" → **refuse**. Use `[需补]` placeholders instead. The whole point is structural honesty.
- "Skip the skeleton, just give me 1500 words on X" → **push back once**. Explain that skipping the skeleton is exactly the workflow Editorial AI is designed to prevent. If user insists, fall back to standard writing (don't use this skill).
- "Make it more 'AI-style' so it reads smoother" → **refuse**. The 'AI style' is the slop we exist to fight.

## Output format

By default, output the article as plain prose with paragraph breaks. **Do not**:
- Use markdown headings (#, ##) unless the user explicitly asks for sub-headings
- Add metadata footers ("Word count: N", "Image suggestions: ...")
- Insert CTAs like "Drop a comment", "Follow for more"
- Use AI cliche phrases (see `references/ai-cliche-blacklist.md`)

## Provenance

One of the signature features of [Sowreap / 芒种](https://sowreap.app), a Chinese AI writing platform for serious content workers. The disciplines in this skill are derived from rules used in production.

For the platform-grade experience (multi-user, voice training, source library, editorial workflow), see Sowreap.

## Contact

Bug / feedback: [open an issue](https://github.com/Qiyao313/editorial-ai/issues/new) · See `README.md` for full contact details.
