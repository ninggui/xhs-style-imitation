# 小红书风格模仿

![GitHub stars](https://img.shields.io/github/stars/ninggui/xhs-style-imitation)
![License](https://img.shields.io/github/license/ninggui/xhs-style-imitation)
[![SkillHub](https://img.shields.io/badge/SkillHub-在线安装-blue)](https://skillhub.cn/skills/xhs-style-imitation)

用户给博主/笔记链接/截图，拉取原笔记分析风格指纹，按指纹写新文章。

## 这是什么

一个可复用的 AI Agent 技能（Skill），来自真实业务场景沉淀，含完整执行流程、避坑清单与验证步骤。

## 快速使用

将本仓库放入 Agent 技能目录后，用对应触发词调用（见 SKILL.md），Agent 会自动加载并执行完整流程。

## 核心能力

| 能力 | 说明 |
|------|------|
| 原笔记拉取与解析 |
| 风格指纹提取（句式/语气/结构） |
| 按指纹生成新文 |

## 使用方式（安装）

- **Hermes**: 放入 `skills/` 目录
- **Claude**: 放入 `~/.claude/skills/`
- **其他 Agent**: 按对应 SKILL.md 格式放入技能目录
- **SkillHub 一键安装**: https://skillhub.cn/skills/xhs-style-imitation

## 优势

- 风格可复现不靠感觉
- 适配账号人设
- 内容合规内置

## 内容结构

- `SKILL.md` — 核心技能定义（触发条件、执行流程、避坑清单）
- `references/` — 可选参考文件

## 许可

MIT
