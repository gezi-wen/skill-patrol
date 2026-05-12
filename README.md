# Skill Patrol · 技能巡检

让任何 AI 编程代理定时扫描 GitHub 生态，发现新工具和技能，推送到微信/飞书/Telegram。

## 这是什么

一个 **平台无关** 的 AI Agent Skill——不绑定 Claude Code，任何支持定时任务的 AI 编程工具都能用。

每周自动扫描你指定的源（GitHub 仓库、排行榜、Reddit、npm），筛选出对你有价值的工具，推送到消息平台。

## 快速开始

### 1. 复制文件

```bash
git clone https://github.com/gezi-wen/skill-patrol.git
cd skill-patrol
```

### 2. 编辑源列表

```bash
cp patrol_state.example.json patrol_state.json
# 编辑 patrol_state.json，修改 sources 数组
```

### 3. 设置定时任务

**cc-connect 用户（微信）:**
```bash
cc-connect cron add --cron "0 7 * * 1" \
  --prompt "执行 skill-patrol: 读取 SKILL.md 的 Patrol 段" \
  --desc "周一技能巡检"
```

**系统 cron 用户:**
```bash
0 7 * * 1 cd /path/to/skill-patrol && claude -p "$(cat prompt.txt)"
```

## 工作原理

```
┌──────────┐    ┌──────────┐    ┌──────────┐
│ 读取状态  │ →  │ 抓取源内容 │ →  │ 四类筛选  │
│ patrol_   │    │ GitHub/   │    │ 运维·文件 │
│ state.json│    │ Reddit/npm│    │ 记忆·有趣 │
└──────────┘    └──────────┘    └──────────┘
                                      ↓
┌──────────┐    ┌──────────┐    ┌──────────┐
│ 更新状态  │ ←  │ 推送微信  │ ←  │ 写入报告  │
│ week+1   │    │ 一条一个  │    │ outbox/   │
└──────────┘    └──────────┘    └──────────┘
```

## 目录

```
skill-patrol/
├── SKILL.md               ← AI 代理执行主文件
├── README.md              ← 本文件
├── patrol_state.example.json ← 状态和源列表示例
├── outbox/                ← 巡检报告
└── LICENSE                ← MIT
```

## 支持的源

- GitHub 仓库和搜索
- 网站排行榜（skills.dev 等）
- Reddit 子版块
- npm 包搜索
- 任何有公开 API 的内容源

## 支持的消息平台

通过 [cc-connect](https://github.com/chenhg5/cc-connect)：微信、企业微信、飞书、Telegram、Slack、Discord、QQ、钉钉、LINE

## 许可

MIT
