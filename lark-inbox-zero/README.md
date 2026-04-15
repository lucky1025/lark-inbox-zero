# lark-inbox-zero

> 今日待办收集 · Inbox Zero for Feishu

一个基于 [lark-cli](https://github.com/larksuite/cli) 的飞书 Skill，自动扫描今日所有消息和会议纪要，提取需要你处理的待办事项。

## 功能

- 🔍 扫描今日所有群聊和私聊消息，筛选 @我 或回复我的消息
- 📅 扫描今日参会，从会议纪要中提取与我相关的任务待办
- 📝 汇总编号列表，用户确认后一键录入飞书任务

## 触发词

> "看看今天有啥还没干的活么"  
> "今天有什么待办"  
> "今日待办"  
> "我今天有哪些事要处理"

## 安装

```bash
# 安装 lark-cli（如尚未安装）
npm install -g @larksuite/cli

# 安装本 skill
# 将 SKILL.md 放入你的 skills 目录即可
```

## 使用方法

当用户说出发送词时，AI Agent 会自动执行以下流程：

### Step 1 — 扫描消息
```bash
lark-cli im +messages-search --is-at-me --chat-type group --start "$START" --end "$END" --page-all --format json
lark-cli im +messages-search --is-at-me --chat-type p2p --start "$START" --end "$END" --page-all --format json
```

### Step 2 — 扫描会议纪要
```bash
lark-cli vc +search --start "$START" --end "$END" --page-all --format json
lark-cli vc +notes --meeting-ids "meeting_id_1,meeting_id_2,..."
lark-cli docs +fetch --doc <note_doc_token>
```

### Step 3 — 用户确认
汇总所有行动项，编号列表发给用户，等待确认。

### Step 4 — 创建任务
```bash
lark-cli task +create --title "<任务标题>" --due "<截止时间>" --as user
```

## 所需权限

| 权限 | 用途 |
|------|------|
| `search:message` | 搜索消息 |
| `vc:meeting.meetingevent:readonly` | 搜索会议记录 |
| `vc:note:read` | 获取会议纪要 |
| `docx:document:readonly` | 读取纪要文档 |
| `task:task:create` | 创建飞书任务 |

授权命令：
```bash
lark-cli auth login --scope "search:message"
lark-cli auth login --scope "vc:meeting.meetingevent:readonly"
lark-cli auth login --scope "vc:note:read"
lark-cli auth login --scope "docx:document:readonly"
lark-cli auth login --scope "task:task:create"
```

## 文件结构

```
skills/lark-inbox-zero/
├── SKILL.md       # Skill 定义文件
└── README.md      # 本文件
```

## 技术栈

- [lark-cli](https://github.com/larksuite/cli) — 飞书官方 CLI 工具
- [lark-im](https://github.com/larksuite/cli/tree/main/skills/lark-im) — 消息管理
- [lark-vc](https://github.com/larksuite/cli/tree/main/skills/lark-vc) — 视频会议
- [lark-task](https://github.com/larksuite/cli/tree/main/skills/lark-task) — 任务管理

## License

MIT
