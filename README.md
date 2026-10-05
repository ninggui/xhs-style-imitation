<img src="./assets/cover.png" alt="小红书风格模仿" width="100%">

<div align="center">

# 小红书风格模仿

**给我一个小红书博主，我把他的"风格指纹"提取出来，让 AI 按这个指纹写新笔记。**

<p>
  <a href="#"><img src="https://img.shields.io/badge/Xiaohongshu-style%20fingerprint-FF2442?logo=xiaohongshu&logoColor=white" alt="XHS" /></a>
  <a href="#"><img src="https://img.shields.io/badge/verified-62%20notes%20batch-pink" alt="62 notes verified" /></a>
  <a href="#"><img src="https://img.shields.io/badge/license-MIT-green" alt="MIT" /></a>
</p>

[解决什么问题](#解决什么问题) · [风格指纹是什么](#风格指纹是什么) · [三路输入](#三路输入) · [快速使用](#快速使用)

</div>

---

## 解决什么问题

你看到一篇爆款小红书笔记，想照着这个风格写自己的内容。但直接说"模仿这个风格"——AI 写出来还是它自己的味。

本工具的做法：先把博主的 20-60 条笔记全量拉下来，提炼出他的**风格指纹**——标题怎么起、开头第一句是什么、段落长度、emoji 密度、结尾怎么收、用什么人称——再按这个指纹写新文章。

实测：蔚来官方号 62 条笔记全量拉取+指纹提炼成功（2026-08-13）。

## 风格指纹是什么

不是"语气像"，而是可量化的 8 个维度：

| 维度 | 提取内容 |
|------|---------|
| 标题 | 句式模板、emoji 用法、数字/疑问比例 |
| 开头 | 第一句怎么钩住人（提问/场景/反差） |
| 段落 | 每段几行、空行频率 |
| emoji | 用哪几个、密度、位置 |
| 人称 | 第一人称/第二人称/无主语 |
| 结尾 | CTA 怎么写、互动引导 |
| 高频词 | 这个博主反复用的词 |
| 禁忌 | 哪些词/句式他绝不用 |

指纹之上还有一层"思维蒸馏"（双引擎）：从语料反向提取作者的价值观 / 论证方式 / 禁忌（思维基因），与写作指纹交叉验证——写出来不只是"像他的语气"，而是"像他会想的"。写前/写中/写后三段约束见 `SKILL.md`。

## 三路输入

**A. 单篇笔记链接（最快）**
```
https://www.xiaohongshu.com/explore/{note_id}?xsec_token={token}
```
链接自带 token，直接拉正文。

**B. 博主名/主页链接**
搜博主 → 拿 userId（必须完整24位）+ xsec_token → 拉全量笔记列表。

**C. 截图/口头描述**
你截图给我，我从截图里提取风格特征。

## 快速使用

```
请使用 xhs-style-imitation。
平台：小红书。
输入A：<粘贴笔记链接>
主题：我要写<你的主题>。
```

## 已验证案例

蔚来官方号 62 条笔记全量拉取 → 风格指纹提炼 → 新笔记生成，详见 `references/nio-case-20260813.md`。

## License

MIT
