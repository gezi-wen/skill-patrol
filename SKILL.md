---
name: skill-patrol
description: 定时巡检 GitHub 生态中的新工具和技能，筛选有价值内容推送到消息平台。支持 Claude Code / Codex CLI / Cursor / Gemini CLI 等多种 AI 编程代理。
type: flexible
---

# Skill Patrol · 技能巡检

每周自动扫描 GitHub 生态，发现新的 AI 编程工具、Skill、MCP Server 和有趣项目，筛选后推送到消息平台。

## 适用平台

本 skill 平台无关——任何支持定时任务和网络搜索的 AI 代理均可使用。

- **Claude Code** — 通过 cc-connect cron 或系统 cron
- **Codex CLI** — 通过 cron 钩子
- **Cursor** — 通过终端调度
- **Gemini CLI** — 通过 scheduled triggers

## 初始设置

### 1. 创建状态文件

在 skill 目录下创建 `patrol_state.json`：

```json
{
  "week": 1,
  "sources": [
    "skills.dev排行榜",
    "punkpeye/awesome-mcp-servers",
    "anthropics/skills官方",
    "hesreallyhim/awesome-claude-code",
    "ComposioHQ/awesome-claude-skills",
    "Reddit r/ClaudeCode",
    "shanraisshan/claude-code-best-practice",
    "npm claude-code新包"
  ]
}
```

`week` 从 1 开始，最大为源列表长度。每次巡检后 +1，到上限回 1。

源列表可按需修改——GitHub 仓库名、网站 URL、Reddit 子版块、npm 搜索均可。

### 2. 配置定时触发

**cc-connect（微信/飞书/Telegram）：**
```bash
cc-connect cron add --cron "0 7 * * 1" --prompt "从 /path/to/SKILL.md 的 Patrol 段开始执行" --desc "周一技能巡检"
```

**系统 cron（Linux）：**
```bash
0 7 * * 1 claude -p "执行 skill-patrol: 读取 /path/to/patrol_state.json，扫描第 week 个源"
```

**自定义调度**：任何能在指定时间触发 AI 代理执行提示词的方式均可。

建议频率：每周 1-3 次，源数量和频率匹配即可。

## 执行流程

> 以下为 AI 代理执行的完整流程。每次触发时执行一次，20 分钟内完成。

### 第一步：读取状态（1 分钟）

读取 `patrol_state.json`，获取当前 `week` 数值和源列表。

找出第 `week` 个源（索引 = week - 1，因为 week 从 1 开始）。本次只扫描这一个源。

### 第二步：获取源内容（最多 7 分钟）

根据源类型选择获取方式：

| 源类型 | 方法 |
|--------|------|
| GitHub 仓库 | `gh api repos/OWNER/REPO/contents` 或 `gh search repos "KEYWORD" --sort stars` |
| 网站/排行榜 | `curl -sL URL` 或 WebFetch |
| Reddit | `curl -sH "Accept: application/json" "https://www.reddit.com/r/SUBREDDIT/new.json"` |
| npm | `npm search "KEYWORD" --json` 或 `gh search repos "TOPIC" --sort updated` |
| 通用 | 先 WebFetch，失败则换 curl 或 API |

优先用 API（有速率限制保护），其次用 WebFetch，最后用 curl。

### 第三步：筛选（最多 7 分钟）

从获取的内容中筛选匹配以下四类的项目：

1. **运维** — 内存/磁盘/监控/自动化/备份。对服务器管理有价值的工具
2. **文件管理** — 整理/分类/去重/批处理/同步。提升日常效率
3. **记忆系统** — memory/context/session/持久化。提升 AI 代理能力
4. **有趣** — 你觉得用户会感兴趣的任何东西。不限于上述分类

筛选标准：
- 近 60 天有更新（活跃维护中）
- 有明确的 README 和使用文档
- 安装和使用成本可接受
- 不与已有推荐重复

### 第四步：写入报告

保存到输出目录的 `patrol_$(date +%Y%m%d).md`：

```markdown
# 技能巡检报告 — YYYY-MM-DD
**扫描源**: [源名称]
**发现数量**: N

---

## [工具名称](链接)

**一句话描述**

💰 代价：
- 内存：[具体占用或估计]
- CPU：[是否有持续开销]
- 依赖：[需要什么额外服务、API key、数据库等]
- 兼容：[是否兼容 DeepSeek/OpenAI 等常用 API]
- 安装复杂度：[简单/中等/复杂，需要什么前置条件]

判定：✅推荐 / ⚠️有条件 / ❌不推荐
```

### 第五步：推送通知

通过消息平台推送每个发现的摘要：

```
🛠 [工具名称]
[一句话描述]
🔗 [链接]
💰 [代价摘要]
✅ [判定]
```

1 个发现也报。只推荐不安装，由用户决定是否尝试。

### 第六步：更新状态

将 `patrol_state.json` 中的 `week` +1。若超过源总数则回 1。

```bash
# 示例更新命令
jq '.week = (.week % [.sources | length] + 1)' patrol_state.json > tmp && mv tmp patrol_state.json
```

## 超时处理

整个过程 20 分钟硬上限。超时后：
- 立即推送当前已筛选的结果（即使不完整）
- 未筛完的源下次继续
- 推送失败记录到日志文件

## 输出目录

```
skill-patrol/
├── SKILL.md              ← 本文件
├── patrol_state.json     ← 状态和源列表
├── outbox/               ← 巡检报告输出
│   └── patrol_20260512.md
└── logs/                 ← 执行日志
    └── patrol.log
```

## 自定义

- **换源**：编辑 `patrol_state.json` 中的 `sources` 数组
- **换频率**：改 cron 表达式
- **换分类**：修改第三步的四个筛选类别
- **换输出格式**：修改第四步的报告模板
- **换消息平台**：cc-connect 支持微信/飞书/Telegram/Slack/Discord/QQ
