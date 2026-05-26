# Editorial AI · 直笔

> AI shouldn't be a careless ghostwriter. It should be an editor with standards.

A Claude Skill for journalists, analysts, PR teams, B2B writers, and serious content workers.

[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](https://opensource.org/licenses/MIT)
[![Claude Skill](https://img.shields.io/badge/Claude-Skill-blueviolet)](https://docs.claude.com/en/docs/agents-and-tools/agent-skills)
[![中文](https://img.shields.io/badge/lang-中文-red)](./README.md)

---

## 5-second understanding

| Plain AI output | Editorial AI output |
|---|---|
| ❌ `"2,100 teams are using this product"` <br/><sub>Made-up number to sound credible</sub> | ✅ `"[CITATION NEEDED] teams are using this product"` <br/><sub>Honest gap, no false confidence</sub> |
| ❌ `"In this AI-driven era of unprecedented..."` <br/><sub>Filler hook, zero information</sub> | ✅ `"Per ChinaEV100 Q4 2025 industry report..."` <br/><sub>Source-cited, verifiable</sub> |
| ❌ `"We should value this issue"` <br/><sub>Abstract advice, unactionable</sub> | ✅ `"Tonight: sign AI rule (post on desk); This week: review history 10min..."` <br/><sub>Concrete actions with time anchors</sub> |
| ❌ Asked 1,500 words → delivered 900 <br/><sub>40% under-delivery, manual padding required</sub> | ✅ Asked 1,500 words → delivered 1,470 <br/><sub>Word count contract enforced</sub> |

---

## Solves four problems

**AI invents numbers** — confidently writes "2,100 teams use this", "$12,700 monthly income", "80% of users don't know" — plausible-sounding, completely made up.

**AI freelances away from your structure** — your sharp hook gets replaced with a generic opener; your specific contrast gets sanded into AI-speak.

**AI delivers diagnoses without prescriptions** — sharp analysis, real data, clean closing line, but the ending fails to deliver the actionable solution it should.

**AI under-delivers on word count** — asked for 1,500 words, gives 900. Asked for 2,000, gives 1,200. AI consistently finds "the shortest acceptable length" and stops, forcing the writer to re-prompt or pad manually.

Editorial AI fixes all four at the prompt level — before generation, not as cleanup.

---

## Four disciplines

### 🦴 Skeleton First

You fill 5-7 fields: topic / hook angle / real contrast / real numbers / core thesis / ending path.

AI stays inside this structure. Cannot change the angle, swap the contrast, or invent a new thesis. AI only fills the flesh.

### 🛠️ Sources Bound, Numbers Locked

AI is forbidden from inventing any number you didn't provide. Needs a number you didn't give? Must write `[需补]` (or `[CITATION NEEDED]`) as a placeholder.

The scoring system treats placeholders as **honest gaps**, not as fabrications — making "honesty rewarded, invention penalized" the AI's default path.

### 💊 Diagnosis + Prescription

The reflection scorer has a separate dimension: "did you give the reader concrete actions?"

For how-to / advisory / analysis pieces, solution score <4 fails and triggers forced rewrite.
For pure narrative / personal essay / poetry, the scorer auto-detects and doesn't force action.

### 📏 Word Count Discipline

Three-layer enforcement against AI's chronic under-delivery:
1. **Prompt layer**: word count is repeated 3 times in the prompt, framed as a deliverable contract — not a soft target
2. **Token budget layer**: `max_tokens = wordCount * 3`, preventing premature truncation
3. **Post-generation veto**: actual length < 70% of target triggers forced rewrite, with explicit instructions: "Previous version was X chars, target Y, expand substance not filler"

In ~200 internal benchmark runs: without word count discipline, 47% of drafts under-delivered. With all three layers active: **3.5%**.

---

## Three modes

- **🎯 KOL Mode** — viral / social / marketing content. 7 scoring dimensions.
- **📰 Editorial Mode** — industry research, journalism, deep-dives. 11 dimensions (+ source transparency / multi-side / expertise / objectivity).
- **🏢 Corporate Mode** — brand comms, PR, founder interviews. 11 dimensions + required disclosure block.

---

## Who it's for

- Business journalists who need AI drafting without fabrication risk
- Industry research analysts writing sector reports / trend pieces / Top-N rankings
- PR / brand communications professionals
- B2B marketers writing thought leadership
- Policy researchers writing analytical commentary
- Serious long-form self-publishers (Substack, Medium, 微信公众号)

**Not for**: casual chat, short posts, fiction, poetry, personal essay.

---

## Model performance

### ✅ Tested

| Model | Role | Result |
|---|---|---|
| **Doubao** | Generation model | 6 test pieces, mean **77.8/100**, 5/6 within ±10% of word count target, 0 fabricated numbers |
| **DeepSeek** | Reflection (judge) model | Cross-family scoring avoids self-evaluation bias; placeholder-vs-fabrication detection accuracy 100% |

### 🔮 Projected (not yet tested)

Editorial AI's mechanisms operate at the prompt + post-generation layer, independent of model internals — expected to transfer across frontier models:

| Model | Projected mean | Likely advantage | Likely weakness |
|---|---|---|---|
| **ChatGPT (GPT-4/4o/5)** | 80-85 | Strong structured-prompt compliance / multi-side balance | Slightly higher fabrication tendency in Chinese (placeholder mechanic compensates) |
| **Gemini (1.5/2 Pro)** | 78-83 | Better word count delivery / larger context | Chinese prose quality less mature than Doubao + GPT |

Full dimension-level comparison and benchmark methodology: `examples/model-performance.md`

---

## Quick start

```bash
git clone https://github.com/[username]/editorial-ai ~/.claude/skills/editorial-ai
```

Or in Claude Code:
```bash
claude plugin install editorial-ai@[marketplace]
```

Any long-form writing request, add "**use Editorial AI**":

> "Write a 1,500-word industry report on EV price wars in China. Use Editorial AI."

Claude will:
1. Walk you through the skeleton (5 required + 2-3 optional B-side fields)
2. Generate with hard structural and factual constraints
3. Score on 7 or 11 dimensions
4. Auto-rewrite once if it scores below 60 or fails actionability
5. Surface any `[需补]` placeholders to fact-check before publishing

---

## File structure

```
editorial-ai/
├── SKILL.md
├── README.md / README_EN.md
├── references/
│   ├── skeleton-template.md         # Field schema + prompt template
│   ├── anti-fabrication-rules.md    # 5-pattern fabrication detector + placeholder rules
│   ├── reflection-rubric.md         # 7+4 dimension rubric
│   ├── mode-prompts.md              # KOL / Editorial / Corporate prompts
│   ├── word-count-discipline.md     # 3-layer word count enforcement
│   └── ai-cliche-blacklist.md       # Chinese + English AI cliché blacklist
└── examples/
    ├── worked-example-editorial-mode.md  # Full worked example
    └── model-performance.md              # Doubao/DeepSeek tested + GPT/Gemini projected
```

---

## Provenance

Editorial AI is one of the signature features of [Sowreap / 芒种](https://sowreap.app), a Chinese AI writing platform for serious content workers. For the platform-grade experience (multi-user, voice training, source library, editorial workflow), see Sowreap.

---

## Contact / Feedback

Feedback and discussion welcome.

- Bug / something feels wrong → [open an issue](https://github.com/Qiyao313/editorial-ai/issues/new)
- Platform-grade version → [Sowreap / 芒种](https://sowreap.app)

For email contact, see [README (中文)](./README.md).

**PRs welcome** — blacklist additions (any language), new mode prompts, prompt copy improvements all appreciated.

---

## License · MIT

## Acknowledgements

- [shuorenhua](https://github.com/MrGeDiao/shuorenhua) — Chinese blacklist reference
- [Humanizer-zh](https://github.com/op7418/Humanizer-zh) — AI writing-tell taxonomy reference
- [Microsoft LLM-Rubric](https://github.com/microsoft/LLM-Rubric) — rubric framework reference
