[README.md](https://github.com/user-attachments/files/27038553/README.md)
<div align="center">
<br/>

# 📬 lark-inbox-zero
### 让你的飞书收件箱归零，不遗漏任何待办
<br/>

<p>
  <a href="LICENSE"><img src="https://img.shields.io/badge/License-MIT-yellow.svg?style=for-the-badge" alt="MIT License" /></a>
  <a href="https://github.com/larksuite/cli"><img src="https://img.shields.io/badge/lark--cli-飞书官方CLI-FF4D4F.svg?style=for-the-badge" alt="lark-cli" /></a>
  <img src="https://img.shields.io/badge/无外部依赖-zero_deps-success?style=for-the-badge" alt="zero deps" />
  <img src="https://img.shields.io/badge/安全-用户确认后录入-blue?style=for-the-badge" alt="safe" />
</p>

```
用户说触发词  →  AI 自动扫描  →  汇总待办清单  →  用户确认  →  一键录入飞书任务
   "今日待办"       消息+会议           过滤噪音         确认序号         闭环
```

**[📦 安装](#-安装)** · **[⚡ 快速开始](#-快速开始)** · **[🏗️ 工作原理](#️-工作原理)** · **[🔐 权限](#-权限-scope)**

</div>
<br/>

---

## 💭 一个典型的周一早上

<table>
<tr>
<td width="50%" valign="top">

### 😩 没有这个工具

```
8:50  打开飞书...
      翻昨晚群聊找 @我的消息
      翻私聊回看有没有漏掉的
      翻会议通知看昨天开了啥会
      等等，昨天那个会议纪要还没看
9:30  "我好像漏了什么事..."
      打开 Excel 手动录入
      @ 这个人确认一下
      @ 那个人确认一下
9:50  算了先开会吧
```

**时间成本**：每天 30 分钟在"找待办"上
**心理成本**：总觉得有什么漏了

</td>
<td width="50%" valign="top">

### 😎 有这个工具

```
8:50  "今日待办"
      → AI 自动扫描今日 @我的消息
      → 自动提取会议纪要中的待办
      → 3 秒生成待办清单

9:00  确认清单：1、2、4、7 要处理
      → 4 个飞书任务自动创建
      → 收到任务通知
9:05  倒一杯咖啡，开始处理真正重要的事
```

**时间成本**：30 秒确认
**心理成本**：所有待办都在飞书任务里，不会漏

</td>
</tr>
</table>

> 职场人每天平均花 **2.5 小时** 在"找信息"上—— 
> 不是信息太多，是**没有把待办从噪音里捞出来的机制**。

---

## ✨ 核心能力

<table>
<tr>
<td width="50%" valign="top">

### 🔍 **消息扫描**
自动扫描今日所有群聊和私聊中 @我 或回复我的消息，提取需要行动的内容。

```
触发词："今日待办" / "今天有什么待办"
```

> 把散落在各处的 @ 变成结构化的待办清单

</td>
<td width="50%" valign="top">

### 📅 **会议待办提取**
从今日参会纪要中识别分配给我的任务和行动项。

```
自动获取：会议列表 → 纪要文档 → 提取待办
```

> 会议开完 = 待办自动入列，不用手动抄

</td>
</tr>
<tr>
<td valign="top">

### 🧹 **智能过滤**
排除纯通知、点赞、确认等无行动需求的消息，只保留真正需要处理的事项。

```
保留：需要回复的 / 需要跟进的 / 需要决策的
过滤：纯通知 / 点赞 / 已完成的确认
```

> AI 帮助过滤，你只看你真正要处理的

</td>
<td valign="top">

### ✅ **确认后录入**
汇总清单供用户确认，用户明确回复后再创建飞书任务，不多建也不少建。

```
用户确认 → 一键创建飞书任务 → 收到任务通知
```

> 你说了算，AI 不自作主张

</td>
</tr>
</table>

---

## 📦 安装

### ✅ 推荐方式：直接对 Agent 说

> **请帮我安装这个 skill：**
> **`https://github.com/lucky1025/lark-inbox-zero.git`**

### 🛠️ 手动安装

```bash
git clone https://github.com/lucky1025/lark-inbox-zero.git
```

把仓库目录放到 Agent 的 skills 扫描路径下即可生效。

### 📋 运行环境

|  | 说明 |
|---|---|
| 🟢 **Node.js 18+** | 仅用于安装 lark-cli（已装可跳过） |
| 🟢 **lark-cli** | 飞书官方 CLI 工具：`npm install -g @larksuite/cli` |
| 🟢 **飞书账号** | 需消息搜索 + 会议 + 任务相关权限 |

---

## ⚡ 快速开始

```bash
# ⓪ 安装 lark-cli
npm install -g @larksuite/cli

# ① 授权所需权限
lark-cli auth login --scope "search:message"
lark-cli auth login --scope "vc:meeting.meetingevent:readonly"
lark-cli auth login --scope "vc:note:read"
lark-cli auth login --scope "docx:document:readonly"
lark-cli auth login --scope "task:task:create"

# ② 开始使用
# 直接对 Agent 说："今日待办"
# Agent 会自动执行扫描 → 汇总 → 确认 → 创建任务
```

---

## 🎬 真实产出一瞥

<details open>
<summary><b>▶ 用户收到待办汇总清单</b></summary>

```
📋 今日待处理（共 4 项）

消息类：
1. 【群聊】来自 [张三]：下周评审材料准备好了吗 — 产品群
2. 【群聊】来自 [李四]：@你 帮忙看下这个方案 — 项目组
3. 【私聊】来自 [王五]：上次说的合同条款确认一下

会议待办：
4. 【会议：Q2 复盘会】撰写技术方案文档 — 负责人：我

请确认哪些需要录入任务（回复序号，如：1、3）？
```

</details>

<details>
<summary><b>▶ 用户确认后自动创建任务</b></summary>

```
✓ 已创建 3 个飞书任务：
  1. [群聊] 待回复：下周评审材料准备好了吗
     → https://example.feishu.cn/task/xxx
  2. [群聊] 待回复：帮忙看下这个方案
     → https://example.feishu.cn/task/xxx
  3. [会议] 待办：撰写技术方案文档
     → https://example.feishu.cn/task/xxx
```

</details>

---

## 🏗️ 工作原理

```
┌─────────────────────────────────────────────────────────────┐
│                    lark-inbox-zero                          │
├─────────────────────────────────────────────────────────────┤
│                                                             │
│  Step 1 ──▶ 扫描消息                                        │
│             ├─▶ 群聊消息（@我的）                           │
│             └─▶ 私聊消息（@我的）                           │
│                                                             │
│  Step 2 ──▶ 扫描会议                                        │
│             ├─▶ 获取今日会议列表                            │
│             └─▶ 提取会议纪要中的待办                        │
│                                                             │
│  Step 3 ──▶ 汇总确认                                        │
│             └─▶ 列出待办清单 → 用户确认                     │
│                                                             │
│  Step 4 ──▶ 创建任务                                        │
│             └─▶ 一键录入飞书任务                            │
│                                                             │
└─────────────────────────────────────────────────────────────┘
```

### 核心流程

| 步骤 | 操作 | 说明 |
|---|---|---|
| Step 1 | 消息扫描 | 搜索今日 @我的群聊/私聊消息 |
| Step 2 | 会议提取 | 获取今日会议纪要，识别待办 |
| Step 3 | 过滤汇总 | 排除噪音，生成待办清单 |
| Step 4 | 用户确认 | 用户选择要录入的序号 |
| Step 5 | 创建任务 | 一键录入飞书任务 |

---

## 🔐 权限 scope

| 操作 | 必需 scope |
|---|---|
| 搜索消息（`im +messages-search`） | `search:message` |
| 搜索会议记录（`vc +search`） | `vc:meeting.meetingevent:readonly` |
| 获取会议纪要（`vc +notes`） | `vc:note:read` |
| 读取纪要文档（`docs +fetch`） | `docx:document:readonly` |
| 创建飞书任务（`task +create`） | `task:task:create` |

---

## 🛡️ 安全原则

|  |  |
|---|---|
| 🔒 | **先确认后录入**：Step 3 必须等用户明确回复，不擅自创建任务 |
| 🔒 | **用户身份执行**：全程使用 `--as user`，确保数据访问完整性 |
| 🔒 | **精确筛选**：只保留需要行动或回应的事项，排除纯通知 |

---

## 📁 目录结构

```
lark-inbox-zero/
├── 📘 SKILL.md              # Skill 定义文件 + 触发描述
├── 📘 README.md             # 本文件
└── 📘 LICENSE               # MIT
```

---

## ❓ FAQ

<details>
<summary><b>触发词有哪些？</b></summary>

以下任一表达都会触发 Skill：

- "看看今天有啥还没干的活么"
- "今天有什么待办"
- "今日待办"
- "我今天有哪些事要处理"

</details>

<details>
<summary><b>会议还没结束，纪要还没出怎么办？</b></summary>

如果今日没有已结束的会议，Skill 会补充查询日历日程（通过 `lark-calendar`），确保不遗漏任何相关任务。

</details>

<details>
<summary><b>任务截止时间怎么确定？</b></summary>

- 如果原消息或会议纪要中有明确截止时间 → 使用该时间
- 如果没有 → 默认截止时间为今日 18:00
- 如果任务无法当天完成 → 与用户确认截止时间

</details>

<details>
<summary><b>数据安全吗？会上传到任何第三方吗？</b></summary>

**完全不会**。所有数据只通过 lark-cli 调飞书 OpenAPI，不经过任何第三方服务器。

</details>

---

## 🛣️ 路线图

- [x] **v1.0** 消息扫描 + 会议待办 + 确认录入
- [ ] **v1.1** 支持关键词补全（如搜索自己名字被提及的消息）
- [ ] **v1.2** 支持周期性重复任务
- [ ] **v2.0** 智能分类 + 优先级排序

---

## 🤝 贡献

欢迎 issue 和 PR！

---

## 📄 License

**[MIT](LICENSE)** © 2026

---

<div align="center">

---

<sub>**让飞书收件箱归零，不遗漏任何待办**</sub>

<sub>如果这个工具帮你省了时间，给个 ⭐ 鼓励一下</sub>

</div>
