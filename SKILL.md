---
name: xhs-style-imitation
description: 小红书风格模仿写作。用户给博主/笔记链接/截图，拉取原笔记分析风格指纹，按指纹写新文章。
---

# 小红书风格模仿写作（XHS Style Imitation）

用户给一个固定小红书博主 或 单篇小红书笔记，要求"模仿这个风格给我写篇文章"时使用。
技术链路已验证（2026-08-13 蔚来官方号62条笔记全量拉取+指纹提炼成功）。

## 触发条件
- "模仿这个博主/笔记的风格写XX"
- 用户发小红书链接（explore/{id} 或 分享文案）
- 用户给博主名/截图，要求按该风格输出

## 三路输入解析

### 输入A：单篇笔记链接（最常用）
小红书笔记链接格式：
```
https://www.xiaohongshu.com/explore/{note_id}?xsec_token={token}&xsec_source=pc_feed
```
提取 `note_id` + `xsec_token` → 直接 `get_feed_detail` 拉正文。**链接自带 token 时无需搜索**，最快路径。

### 输入B：博主名/主页链接
1. `search_feeds` 搜博主名（如"蔚来"）→ 结果里找目标博主的笔记 → 提取该博主的 `userId`（完整24位）+ 任一条笔记的 `xsecToken`
2. `user_profile`（user_id + xsec_token + tab:note）→ 拉该博主全部笔记列表（含标题+互动）
3. 注意：`user_profile` **必须传 xsec_token**，否则报 `invalid params: missing properties: ["xsec_token"]`；userId 必须完整24位（截短会导致匹配0条）

### 输入C：用户截图/口头描述
- 用户发截图 → 图可能无法 vision 识别（当前模型无 vision 能力）→ 请用户补发链接或复制正文文字
- 用户口头描述风格 → 直接按描述提炼，无需拉取

## 拉取正文（get_feed_detail）

选3-8篇代表性笔记（覆盖不同内容类型）拉完整正文：

```bash
# payload 写到文件再 curl（中文避免转义问题）
cat > /path/to/data/xhs_style_payload.json << 'EOF'
{"jsonrpc":"2.0","method":"tools/call","params":{"name":"get_feed_detail","arguments":{"feed_id":"{note_id}","xsec_token":"{token}"}},"id":1}
EOF
docker cp /path/to/data/xhs_style_payload.json xhs-mcp:/tmp/payload.json
docker exec xhs-mcp sh -c "curl -s 'http://127.0.0.1:<port>/mcp' -H 'Content-Type: application/json' -d @/tmp/payload.json --max-time 90"
```

- 正文在 `result.content[0].text` → 内层 JSON → `data.note.title` / `data.note.desc`
- **token 轮换**：xsecToken 会过期（LB前缀失效），失效时重新 `user_profile` 或 `search_feeds` 刷新
- 拉取间隔 ≥3s，避免风控

## 风格指纹提炼模板（10维度）

从原文提炼以下维度，输出成指纹表：

| 维度 | 观察点 | 示例 |
|------|--------|------|
| 1. 人称视角 | 我/我们/无主语/你 | "我"=个人号，"无主语"=官方号 |
| 2. 句式长度 | 长句论证/短句快节奏/混合 | 小红书爆款=短句多 |
| 3. 标点习惯 | 感叹号/问句/省略号/分点符号 | 开头问句=悬念型 |
| 4. 词汇偏好 | 口头禅/高频词/专业术语 | 蔚来："日拱一卒""一起加电" |
| 5. 情绪基调 | 热情/理性/幽默/治愈/焦虑 | 情绪是风格的底色 |
| 6. 结构模式 | 开头hook→正文→结尾CTA | 数据型/故事型/清单型 |
| 7. 话题角度 | 讲什么、从什么角度切 | 避坑/种草/经验/情绪 |
| 8. 数字使用 | 精确/模糊/对比/换算 | 换算成用户价值=高传播 |
| 9. 互动设计 | 提问/投票/评论区引导 | "你们呢？""评论区聊聊" |
| 10. 排版习惯 | emoji/分隔线/加粗/分点 | 🔸✅⭐ 等固定符号 |

## 写作流程

1. 确认场景：用户要写什么主题？（如果用户没说，先问）
2. 拉语料：按三路输入解析 → 拉取3-8篇正文 → 提炼10维指纹
3. 套指纹写初稿：完全用目标博主的句式/用词/结构/情绪，但主题是用户的新主题
4. 按 humanizer-zh 去AI味（去掉"首先/其次/总之"、模板腔、完美对称句）
5. 交付：正文 + 3个标题备选（若用户要发小红书，标题≤20字含标点）

## 关键技巧

- **不是抄内容，是抄"表达方式"**：句式节奏、用词习惯、情绪曲线、结构骨架
- **保留博主的口头禅/固定收尾**（如蔚来"一起加电！"）——这是风格识别度最高的部分
- **数据必须真实**：模仿风格可以，编数据不行（不确定就标占位符或问用户）
- **多篇对比**：至少看3篇才能避免被单篇带偏（博主不同内容类型风格有差异）
- **去AI味是必须步骤**：AI默认输出完美对称、过度排比，与真人博主风格天然冲突

## 已实例化案例

- **nio-brand-voice**（蔚来专用，v2.2）：李斌/秦力洪/小红书官方号三套指纹已固化，含62条官方笔记语料。写蔚来相关直接用它，不用重新拉取。
- 其他博主可按本 skill 流程随时实例化（如"模仿XX博主写买房笔记"）。

## 输出格式
```
【风格指纹】
表格（10维）

【成稿】
标题：XX
正文：XX

【可选】3个标题备选
```
