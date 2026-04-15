---
name: lark-inbox-zero
version: 1.0.0
description: "今日待办收集：扫描今日所有群聊和私聊消息（@我的、回复我的），以及今日会议的纪要，从中提取需要采取行动或给出回应的事项，汇总后请用户确认，再将确认的事项录入飞书任务。当用户说「看看今天有啥还没干的活么」「今天有什么待办」「今日待办」「我今天有哪些事要处理」或类似表达时使用。"
metadata:
  requires:
    bins: ["lark-cli"]
  cliHelp: "lark-cli im --help && lark-cli vc --help && lark-cli task --help"
---

# lark-inbox-zero (v1.0.0)

**今日待办收集 · Inbox Zero for Feishu**

> **前置条件：** 先用 Read 工具读取 [`../lark-shared/SKILL.md`](../lark-shared/SKILL.md)，了解认证、身份切换（`--as user`）、权限处理。

---

## 工作流程总览

```
Step 1  lark-im +messages-search   扫今日所有 @我 / 回复我的消息
Step 2  lark-vc +search +notes     扫今日所有参会，从纪要中提取待办
Step 3  汇总 → 用户确认             列出所有需要行动的事项
Step 4  lark-task +create          根据确认结果，创建飞书任务
```

**身份：** 全程使用 `--as user`（用户身份）

---

## Step 1 — 扫描今日消息

### 1.1 获取当天时间范围

```bash
TODAY=$(date +%Y-%m-%d)
START="${TODAY}T00:00:00+08:00"
END="${TODAY}T23:59:59+08:00"
```

### 1.2 搜索 @我的群聊消息

```bash
lark-cli im +messages-search \
  --is-at-me \
  --chat-type group \
  --start "$START" \
  --end "$END" \
  --page-all \
  --format json
```

### 1.3 搜索 @我的私聊消息

```bash
lark-cli im +messages-search \
  --is-at-me \
  --chat-type p2p \
  --start "$START" \
  --end "$END" \
  --page-all \
  --format json
```

### 1.4 搜索提及我名字或回复我的消息（补充）

如果上述结果太少，再用关键词补全：

```bash
# 以自己名字作为关键词搜索（需已知用户姓名，或先从 contact 获取）
lark-cli im +messages-search \
  --query "用户姓名" \
  --start "$START" \
  --end "$END" \
  --page-all \
  --format json
```

### 1.5 提取行动项

从上述消息结果中筛选出**需要采取行动或给出回应**的消息，排除：
- 纯确认/点赞类回复
- 纯通知类消息（不要求回复的）
- 已完成确认无需跟进的

记录每条消息的：
- `message_id`（来源追溯）
- `chat_name` / `chat_type`（来自哪个会话）
- `content`（消息内容）
- `sender.name`（谁发的）
- `create_time`（时间）

---

## Step 2 — 扫描今日会议纪要

### 2.1 搜索今日所有已结束的会议

```bash
lark-cli vc +search \
  --start "$START" \
  --end "$END" \
  --page-all \
  --format json
```

> **注意：** 如果今日没有已结束的会议（只开了日程还没结束），用 `lark-calendar` 技能补充查询今日的日历日程。

### 2.2 获取每个会议的纪要

从 Step 2.1 结果中提取 `meeting_id`，批量查询纪要：

```bash
# 最多 50 个 meeting_id，逗号分隔
lark-cli vc +notes \
  --meeting-ids "meeting_id_1,meeting_id_2,..." \
  --format json
```

### 2.3 提取待办事项

从纪要结果中提取 `note_doc_token`，获取纪要文档内容：

```bash
lark-cli docs +fetch --doc <note_doc_token>
```

从文档中识别出**与我相关的待办**：
- 分配给我的任务（提及我名字的任务）
- 负责人为我的事项
- 需要我给出意见/评审的内容

---

## Step 3 — 汇总并请用户确认

将 Step 1 和 Step 2 收集到的所有事项，以**编号列表**格式汇总发给用户：

```
📋 今日待处理（共 N 项）

消息类：
1. 【群聊】来自 [张三]：[消息内容摘要] — [群名称]
2. 【私聊】来自 [李四]：[消息内容摘要]

会议待办：
3. 【会议：XXX】[任务描述] — 来自 [王五]
...

请确认哪些需要录入任务（回复序号，如：1、3、5）？
```

**要求用户明确回复要录入哪些**，不要擅自创建。

---

## Step 4 — 创建飞书任务

根据用户确认的序号，使用 `lark-task +create` 逐一创建任务：

```bash
lark-cli task +create \
  --title "<任务标题>" \
  --due "<截止时间（YYYY-MM-DD）>" \
  --as user
```

**任务标题规范：**
- 来自消息：「[群名/私聊] 待回复：[消息摘要]」
- 来自会议待办：「[会议名] 待办：[任务内容]」

**截止时间处理：**
- 如果原消息或会议纪要中有明确的截止时间，使用该时间
- 如果没有，默认截止时间为今日 18:00
- 如果任务无法当天完成，可与用户确认截止时间

**创建完成后：** 将创建成功的任务链接（`url` 字段）汇总发给用户。

---

## 权限清单

| 操作 | 所需 scope |
|------|-----------|
| 搜索消息（`im +messages-search`） | `search:message` |
| 搜索会议记录（`vc +search`） | `vc:meeting.meetingevent:readonly` |
| 获取会议纪要（`vc +notes`） | `vc:note:read` |
| 读取纪要文档（`docs +fetch`） | `docx:document:readonly` |
| 创建飞书任务（`task +create`） | `task:task:create` |

> **授权命令：**
> ```bash
> lark-cli auth login --scope "search:message"
> lark-cli auth login --scope "vc:meeting.meetingevent:readonly"
> lark-cli auth login --scope "vc:note:read"
> lark-cli auth login --scope "docx:document:readonly"
> lark-cli auth login --scope "task:task:create"
> ```

---

## 关键原则

1. **用户身份** — 全程使用 `--as user`，确保能访问用户的全部消息和会议
2. **先汇总后录入** — Step 3 必须等用户确认，不要在用户确认前擅自创建任务
3. **精确筛选** — 排除纯通知类消息，只保留需要行动或回应的事项
4. **完整分页** — 消息搜索使用 `--page-all` 确保不遗漏
5. **今日判断** — 以服务器返回的 `create_time` 为准，过滤出当天消息
6. **任务链接** — 创建任务后必须附上飞书任务链接，方便用户直接跳转
