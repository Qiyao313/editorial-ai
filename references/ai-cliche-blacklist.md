# AI Cliché Blacklist · AI 范文味黑名单

> Phrases the AI must never use. These are "AI tells" — the giveaways that a piece was machine-written. Editorial AI bans them at the prompt level (AI doesn't generate them) and at the reflection level (using one penalizes the punchline / objectivity dimensions).

## Why a blacklist

After scoring thousands of AI-written pieces, certain phrases reliably mark "AI slop":
- Authors don't actually talk this way (no human reaches for "in this rapidly evolving era of...")
- They're filler, not signal — the piece is the same with them removed
- They trigger reader fatigue ("I've read this exact sentence in 50 other AI articles")

This blacklist isn't comprehensive — it's a starting point. The skill is designed to learn user-specific cliché patterns over time.

## Chinese cliché blacklist (高优先级)

### Filler phrases (强制扣分)
- 让我们一起 (let's together)
- 在这个 X 的时代 (in this era of X)
- 值得注意的是 (it's worth noting)
- 综上所述 / 综合所述 (in summary / to summarize)
- 不仅是 X 更是 Y (not only X but also Y)
- 深入探讨 (deeply discuss)
- 关键转折点 (key turning point)
- 赋能 (empower / enable)
- 彰显 (manifest)
- 本质上 (essentially)
- 说白了 (frankly)
- 意味着什么 (what does it mean)
- 不可否认 (undeniable)
- 顺势而为 (go with the flow / momentum)
- 不破不立 (no breaking, no building)
- 站在风口上 (standing in the wind)

### Clickbait headline patterns
- 你是不是也... (Are you also...)
- 99% 的人不知道 (99% don't know)
- 震惊 / 颠覆 / 逆袭 / 真相 / 秘密 / 翻车 / 踩坑 / 反转 / 惊呆
- 学会这 X 招 (learn these X tricks)
- 干货满满 (full of value)
- 保姆级 (nanny-level / step-by-step)
- 必看 / 必收藏 / 必转发

### CTA boilerplate
- 评论区聊两句 (let's chat in the comments)
- 想知道更多关注我 (follow for more)
- 欢迎评论 / 留言 (welcome to comment)
- 下次说 / 下期再聊 / 我们下次见 (talk next time / see you)
- 想知道下集吗 (want to know what's next?)

### B-side specific banned (Editorial / Corporate modes)
- 行业人士透露 (industry sources reveal) — too vague, must name source
- 据了解 (according to learning) — must specify how learned
- 有数据显示 (data shows) — must cite the dataset
- 业内普遍认为 (industry generally believes) — who specifically?
- 不少人指出 (many point out) — name them or skip

## English cliché blacklist

### Filler phrases
- "Let's dive in"
- "In today's rapidly evolving / fast-paced landscape"
- "It's worth noting that"
- "It's important to note"
- "Without further ado"
- "In conclusion / To summarize / In summary"
- "Last but not least"
- "Each with its own unique"
- "Plays a crucial role"
- "Stands as a testament to"
- "Game-changer / Game-changing"
- "Revolutionize / Revolutionary"
- "Leverage" (as a verb)
- "Unleash"
- "Synergize / Synergies"
- "Move the needle"
- "At the end of the day"

### Clickbait patterns
- "You won't believe..."
- "This one trick..."
- "Number N will shock you"
- "What they don't want you to know"

### CTA boilerplate
- "Drop a comment below"
- "Smash that like button"
- "Subscribe for more"
- "Stay tuned"
- "More on that later"

## Detection mechanism

The reflection AI runs this check:

1. Lowercase the article
2. For each banned phrase, count occurrences
3. Map each occurrence to a dimension penalty:
   - Filler phrases → punchline -1 (max -3)
   - Clickbait headline → hook -2 (max -4 if multiple)
   - CTA boilerplate → ending -2
   - Vague B-side attribution → sourceTransparency -2

4. Surface flags in `topicAlignment.reason`:
   - "uses AI cliche phrases (N detected)" if any banned phrase found
   - List the top 3 worst offenders in the reasoning

## Extension mechanism

Users can extend the blacklist for their own context:

```
# In the skeleton's doNotDo field
"do not use: '生态闭环', '弯道超车', '降维打击', '韭菜' (industry-specific clichés)"
```

The AI prepends these to the global blacklist for that one generation.

## Acknowledgements

This blacklist draws from:
- [shuorenhua](https://github.com/MrGeDiao/shuorenhua) — 210+ Chinese banned phrases (we adopt a curated subset)
- [Humanizer-zh](https://github.com/op7418/Humanizer-zh) — 24 AI-writing-tell categories (similar philosophy)
- Common patterns observed across long-form Chinese AI output

If you want to contribute to the blacklist, open a PR with:
- The phrase
- Why it's an AI tell (≤1 sentence)
- Recommended dimension penalty
