# Skill Patrol · 技能巡检

> 让任何 AI 编程代理定时扫描 GitHub 生态，发现新工具和技能，推送到微信/飞书/Telegram。

> A platform-agnostic AI agent skill that periodically scans GitHub for new tools and pushes discoveries to your messaging platform.

---

## 这是什么 / What is this

一个不绑定 Claude Code 的 AI Agent Skill——任何支持定时任务的 AI 编程工具都能用。

每周自动扫描你指定的源（GitHub 仓库、Reddit、npm），筛选出对你有价值的工具，推送到消息平台。

A platform-independent skill — works with any AI coding agent that supports scheduled tasks. Scans your chosen sources weekly, filters useful tools, and pushes to your messaging platform.

**支持的消息平台 / Supported Messaging Platforms**: 微信 WeChat · 飞书 Feishu · Telegram · Slack · Discord · QQ · 钉钉 DingTalk · LINE

---

## 支持源 / Sources

| 类型 Type | 示例 Example |
|-----------|-------------|
| GitHub 仓库 Repo | `awesome-claude-skills` |
| GitHub 搜索 Search | `claude-code skill` |
| 排行榜 Leaderboard | `skills.dev` |
| Reddit | `r/ClaudeCode` |
| npm | `claude-code` packages |

---

## 快速开始 / Quick Start

### 1. 复制并配置 / Clone & Configure

```bash
git clone https://github.com/gezi-wen/skill-patrol.git
cd skill-patrol
cp patrol_state.example.json patrol_state.json
# 编辑 patrol_state.json，修改 sources 数组
# Edit patrol_state.json to customize your sources
```

### 2. 设置定时任务 / Schedule

**cc-connect:**
```bash
cc-connect cron add --cron "0 7 * * 1" \
  --prompt "执行 skill-patrol: 读取 SKILL.md" \
  --desc "周一技能巡检 Monday Patrol"
```

**System cron:**
```bash
0 7 * * 1 cd /path/to/skill-patrol && claude -p "$(cat prompt.txt)"
```

---

## 工作流程 / Workflow

```
┌──────────┐    ┌──────────┐    ┌──────────┐
│ 读取状态  │ →  │ 抓取源内容 │ →  │ 四类筛选  │
│ State     │    │ Fetch     │    │ Filter    │
└──────────┘    └──────────┘    └──────────┘
                                     ↓
┌──────────┐    ┌──────────┐    ┌──────────┐
│ 更新状态  │ ←  │ 推送通知  │ ←  │ 写入报告  │
│ Update    │    │ Notify    │    │ Report    │
└──────────┘    └──────────┘    └──────────┘
```

---

## 筛选类别 / Filter Categories

| 类别 Category | 说明 Description |
|---------------|-----------------|
| 🔧 运维 Ops | 内存/磁盘/监控/自动化 Monitoring & Automation |
| 📁 文件管理 Files | 整理/分类/去重/同步 Organization & Sync |
| 🧠 记忆系统 Memory | AI 代理持久化/上下文 Persistent memory |
| 💡 有趣 Interesting | 你觉得用户会感兴趣的 Anything interesting |

---

## 目录结构 / Structure

```
skill-patrol/
├── SKILL.md                     ← AI 代理执行主文件 Main instruction
├── README.md                    ← 本文件 This file
├── patrol_state.example.json   ← 状态和源列表示例 Example config
├── outbox/                      ← 巡检报告 Reports
└── LICENSE                      ← MIT
```

## License

MIT
