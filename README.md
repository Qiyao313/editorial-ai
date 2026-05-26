# 直笔 · Editorial AI

> AI 不应该是无脑写手, 应该是有底线的编辑。

一个给认真写稿的人用的 Claude Skill — 记者、行业研究员、公关、B2B 内容人、行业自媒体。

[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](https://opensource.org/licenses/MIT)
[![Claude Skill](https://img.shields.io/badge/Claude-Skill-blueviolet)](https://docs.claude.com/en/docs/agents-and-tools/agent-skills)
[![English](https://img.shields.io/badge/lang-English-blue)](./README_EN.md)

---

## 5 秒看懂

| 普通 AI 输出 | 直笔输出 |
|---|---|
| ❌ "2100 个团队在用这个产品" <br/><sub>编了数字, 听着真但无据</sub> | ✅ "[需补] 个团队在用这个产品" <br/><sub>占位符, 不假装知道</sub> |
| ❌ "在这个 AI 飞速发展的时代..." <br/><sub>套话开头, 0 信息</sub> | ✅ "据 ChinaEV100 2025 Q4 报告..." <br/><sub>标具体出处, 可核查</sub> |
| ❌ "我们应该重视这个问题" <br/><sub>抽象建议, 落不了地</sub> | ✅ "今晚: 签 AI 禁令贴书桌; 本周: 翻 AI 历史 10 分钟..." <br/><sub>具体动作 + 时间锚</sub> |
| ❌ 要 1500 字, AI 给 900 <br/><sub>40% 偷工减料, 用户手动补</sub> | ✅ 要 1500 字 → 实际 1470 <br/><sub>字数 contract 强制达标</sub> |

---

## 这个 skill 解决四个问题

**AI 编造数据** — AI 会莫名自信地编造类似 "2100 多个团队在用"、"月入 12.7 万"、"80% 的人不知道" — 这些经常出现在文章中的"数据"很多是编造的, 缺少信源。

**AI 自由发挥** — 作者拟定的钩子被换成平庸开头, 大量内容被稀释成 AI 模式的套话。

**AI 给诊断不给处方** — AI 的文章前半部分分析漂亮、中段内容丰满、收尾锐利, 但是缺少结尾部分应该给出的解决方案。

**AI 字数偷工减料** — 要 1500 字, 给 900; 要 2000 字, 给 1200。AI 永远在"找最低可接受长度交差", 用户被迫返工补写。

这个 skill 目的在 prompt 层面解决这四件事 — 把问题解决在生成之前, 不是事后补救。

---

## 四大机制

### 🦴 骨架先行

预先为一篇文章规划出结构, 用户只需要填 5-7 个字段定结构: 主题 / 钩子角度 / 真实反差 / 真实数据 / 核心论点 / 结尾路径。

AI 必须在这个结构里写, 不许换角度, 不许编新论点。AI 只负责丰满内容, 交付成稿。

### 🛠️ 信源绑定, 数字封锁

AI 被禁止编造没给的任何数字。如果需要数字, 可以要求 AI 检索并提供信源。

输出前会有一套评分体系, 让"诚实有奖, 编造扣分"成为 AI 的默认路径。

### 💊 诊断 + 处方

自评机制独立打分: "是否在文章结尾处给予具体可执行的建议或者方案?"

教程 / 利弊分析 / 建议类稿件, 方案维度 <4 分自动判不合格强制重写。
纯叙事 / 抒情散文 / 诗歌, 体系自动识别不强加此方案。

### 📏 字数稳定

三层约束防止 AI 字数偷工减料:
1. **prompt 层**: 字数在 prompt 中重复 3 次, 框成 deliverable contract, 不是 soft target
2. **token budget 层**: `max_tokens = wordCount * 3`, 防止中途截断
3. **后置否决层**: 实际字数 < 70% 目标 → 强制重写, 重写 prompt 明确告知"上一版 X 字, 必须扩到 Y 字, 扩内容不掺水"

在 ~200 次内部基准测试中: 不带字数约束时 47% 稿件不达标; 三层全开后**3.5%** 不达标。

---

## 三种模式

- **🎯 KOL 模式** — 自媒体 / 营销 / 流量内容。7 维评分。
- **📰 机构媒体模式** — 行业研究 / 调研报告 / 深度报道。11 维 (+信源透明度 / 多方平衡 / 专业度 / 客观性)。
- **🏢 企业品牌模式** — 品牌 / 公关 / 创始人访谈。11 维 + 必须包含利益声明。

---

## 适合用户

- 商业记者: 想用 AI 但不想当编数字的帮凶
- 行业研究员: 写赛道报告 / 趋势分析 / Top N 排名
- 公关 / 品牌传播: 写创始人访谈 / 战略报道 / 危机响应
- B2B 营销: 写 thought leadership
- 政策研究员: 写分析性评论
- 行业自媒体: Substack / Medium / 微信公众号长文

不适合: 闲聊 / 短帖子 / 小说 / 诗歌 / 纯个人散文。

---

## 模型表现

### ✅ 已实测

| 模型 | 角色 | 测试结论 |
|---|---|---|
| **Doubao 豆包** | 写作模型 | 6 次测试均分 **77.8/100**, 5/6 字数达标 ±10%, 0 次编数字 |
| **DeepSeek** | 评分模型 | 跨模型族避免自评偏差; 占位符 vs 编造识别准确率 100% |

### 🔮 预期表现 (未实测, 仅作展望)

直笔的机制全部在 prompt + 后置评分层, 跟模型内部无关, 预期跨模型通用:

| 模型 | 预期均分 | 优势预测 | 弱势预测 |
|---|---|---|---|
| **ChatGPT (GPT-4/4o/5)** | 80-85 | 结构 prompt 遵守好 / 多方平衡强 | 中文场景下编数字倾向略高 (占位符机制补偿) |
| **Gemini (1.5/2 Pro)** | 78-83 | 字数控制更好 / context window 大 | Chinese prose 成熟度不及 Doubao + GPT |

完整分维度对比和测试方法: `examples/model-performance.md`

---

## 5 分钟上手

```bash
git clone https://github.com/[username]/editorial-ai ~/.claude/skills/editorial-ai
```

或在 Claude Code:
```bash
claude plugin install editorial-ai@[marketplace]
```

任何长文写作请求, 加 "**用直笔**":

> "我想写一篇关于中国新能源车 2025 年价格战的行业报告, 1500 字。用直笔。"

Claude 会:
1. 让你填骨架 (5 必填 + 2-3 B 端可选)
2. 按硬约束生成稿子
3. 打 7 维或 11 维评分
4. 不及格 / 缺方案自动重写一次
5. 告诉你稿子里有几个 `[需补]` 待补

---

## 文件结构

```
editorial-ai/
├── SKILL.md
├── README.md / README_EN.md
├── references/
│   ├── skeleton-template.md         # 字段 schema + prompt 模板
│   ├── anti-fabrication-rules.md    # 5 类编造识别 + 占位符规则
│   ├── reflection-rubric.md         # 7+4 维评分 rubric
│   ├── mode-prompts.md              # 三模式 prompt
│   ├── word-count-discipline.md     # 字数三层约束机制
│   └── ai-cliche-blacklist.md       # 中英文 AI 套话黑名单
└── examples/
    ├── worked-example-editorial-mode.md  # 完整实例
    └── model-performance.md              # Doubao/DeepSeek 实测 + GPT/Gemini 展望
```

---

## 出处

直笔是 [芒种 sowreap.app](https://sowreap.app) 的特色功能之一 — 芒种是一个中文 AI 写作平台, 服务于认真写稿的人。如果想要平台级体验 (多用户 / voice 训练 / 素材库 / 编辑工作流), 看芒种。

---

## 联系 / 反馈

欢迎反馈和讨论。

- 发现 bug / 用得不顺手 → [开 issue](https://github.com/Qiyao313/editorial-ai/issues/new)
- 邮件联系 → **3359795@qq.com**
- 平台级版本 → [芒种 sowreap.app](https://sowreap.app)

欢迎 PR — 加新的中文 AI 套话黑名单 / 加新 mode prompt / 加新语种 / 优化提示文案。

### 支持作者

如果直笔帮你少了几次返工, 欢迎请作者喝杯咖啡 ☕ — 微信扫码:

<img src="./assets/wechat-qr.png" alt="微信打赏二维码" width="220"/>

---

## 许可证 · MIT

## 鸣谢

- [shuorenhua](https://github.com/MrGeDiao/shuorenhua) — 中文黑名单参考
- [Humanizer-zh](https://github.com/op7418/Humanizer-zh) — AI 写作痕迹分类参考
- [Microsoft LLM-Rubric](https://github.com/microsoft/LLM-Rubric) — 评分框架参考
