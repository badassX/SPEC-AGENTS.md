# SPEC-AGENTS.md

## Overview

This project is inspired by Spec-kit, OpenSpec, and Stack Workflow. It combines the advantages of spec-driven development and phased development. With a small amount of configuration, it helps everyday developers, especially in “vibe coding” sessions, improve the accuracy of AI execution, reduce repeated copy-pasting into the AI, and still enjoy the stability and convenience of mature software engineering workflows.

## Features

- Zero configuration
- Natural-language interaction
- Small footprint, token efficient
- Production-grade software development workflow
- Supports multiple coding tools
- Project-level memory

Using SPEC-AGENTS effectively gives your development project “memory”. You no longer need to worry that switching tools will make it hard for the AI to understand where you are in the process. As long as the AI reads the documents, it can quickly recover the current project context and development progress.

## Principles

Similar to OpenSpec, SPEC-AGENTS.md splits development into multiple phases and standardizes a “doc-driven development” workflow:

1. First, write `spec_*`, `plan_*`, `task_*` and other docs in natural language under `.phrase/phases/`.  
2. Then, let the agent implement and verify tasks strictly according to those documents.  
3. Finally, write back to `task_*`, `change_*`, `spec_*`, `issue_*`, `adr_*`, so every change can be traced end-to-end.

When you place an `AGENTS.md` file in a project, any AI tool that supports this protocol will read the rules at the start of a session and treat them as “instructions for the developer assistant”:

- All requirements, interactions, and interface information come from docs, not ad-hoc chat messages.  
- Each conversation focuses on a single atomic `taskNNN`.  
- After each task is completed, the assistant records how it was verified and what it affected in the change docs.  

With this set of constraints, SPEC-AGENTS compresses the traditional spec / task / change / issue workflow into a lightweight text protocol, so individuals and small teams can enjoy mature software engineering practice even while “vibe coding”.

## Workflow (How It Works)

The diagram below shows a typical workflow for SPEC-AGENTS.md within a project (ASCII diagram in English, with brief comments):

```
┌────────────────────┐      Draft / update specs (write spec_/plan_ in natural language)
│ Specs & Plans      │
│ (.phrase/phases/   │
│  spec_/plan_ docs) │
└────────┬───────────┘
         │ define intent & scope
         ▼
┌────────────────────┐      Break down into atomic tasks (taskNNN)
│ Atomic taskNNN     │◀──── feedback loop ──────┐  refine task boundaries with the AI
└────────┬───────────┘                          │
         │ agreed task boundaries               │ both sides agree on scope & acceptance
         ▼                                      │
┌────────────────────┐                          │ Implement & verify (AI writes code + tests)
│ Implement & Verify │──────────────────────────┘
│ (AI writes code &  │
│  runs tests)       │
└────────┬───────────┘
         │ mark taskNNN done
         ▼
┌────────────────────┐      Archive changes & update docs
│ Archive & Update   │
│ (change_/spec_/    │
│  issue_/adr_)      │
└────────────────────┘
```

In short:

1. Agree with the LLM to enter a new development phase, then draft or update specs and plans (`spec_/plan_`) in natural language under `.phrase/phases/`.  
2. Work with the AI to break the work into atomic `taskNNN` items, iterating until scope and acceptance criteria are clear.  
3. Implement and verify strictly following the docs, with each conversation focusing on a single task.  
4. When done, write back to `task_*`, `change_*`, `spec_*`, `issue_*`, `adr_*`, so every code change can be traced back to its original written intent.

## Real-world Example

Here is a minimal natural-language dialogue that shows how SPEC-AGENTS.md works in a real session:

**1. User describes a request**

> User:  
> I want to add a “Dark Mode” toggle to the project that can be switched via menu or shortcut. Please guide me through this once using the SPEC-AGENTS workflow.

**2. The AI goes back to the docs and locks in a task**

> AI:  
> Sure. I won’t write code yet. According to `AGENTS.md`, I’ll first update the docs under the current phase:  
> - In `.phrase/phases/phase-ui-20250101/plan_ui.md`, I’ll add this milestone: support dark-mode toggling.  
> - In `.phrase/phases/phase-ui-20250101/task_ui.md`, I’ll add:  
>   - `task003 [ ] Dark mode toggle (verification: manual toggle + UI refresh check)`.  
> Once you confirm this task description and verification method, we’ll move on to implementation.

**3. Align task boundaries**

> User:  
> That works. We don’t need persistence yet; as long as I can toggle it back and forth in the current session, that’s enough.
>
> AI:  
> Got it. I’ll change the verification for `task003` to “manually toggle twice and confirm the UI updates correctly”, and leave persistence for a follow-up `task004`.

**4. AI implements and verifies**

> (Roughly what the AI does behind the scenes)  
> 1. Locate the UI entry point in the code (for example, window or menu definitions under `App/` or `UI/`).  
> 2. Add a “Dark Mode” menu item or shortcut handler following the existing style.  
> 3. Reuse or introduce the core theme-switching logic (ideally in the Core/Shared layer).  
> 4. Run tests and/or start the app manually, toggling dark mode twice according to `task003` and recording the results.

**5. Write back to the docs and close the loop**

> AI:  
> Implementation and verification are complete. Following `AGENTS.md`, I’ll write back to the docs:  
> - In `.phrase/phases/phase-ui-20250101/task_ui.md`, mark `task003` as `[x]`.  
> - In `.phrase/phases/phase-ui-20250101/change_ui.md`, add a record describing the date, file paths, Add/Modify type, behavioral impact, and risks.  
> - Update `spec_ui.md` if needed to reflect the “user action → feedback → fallback” flow.  
> After that, we can open a new `task004` specifically for “remembering the dark-mode setting”.

## Usage

### Using Codex

Copy `SPEC-AGENTS.md` into your project folder and rename it to `AGENTS.md`. Codex will automatically read this file and follow the specification.

### Using Claude Code

In your project folder, edit `CLAUDE.md` to contain:

```text
@AGENTS.md
```

### Using Gemini CLI

In your project folder, edit `GEMINI.md` to contain:

```text
@AGENTS.md
```

---

## 中文说明

本项目受 Spec-kit、OpenSpec、Stack Workflow 启发而来，兼具「规范驱动开发」和「阶段性开发」的优势，通过简单的配置，让普通小白在氛围编程（Vibe Coding）时，提升 AI 执行任务的准确性，减少与 AI 重复输（chao）入（jia）的次数，同时享受成熟的软件开发流程的稳定、便利。

## 特点
- 零配置
- 自然语言沟通
- 体积小，节省 TOKEN
- 成熟的软件项目开发流程 
- 支持多个编程工具
- 项目记忆

使用 SPEC-AGENTS 还相当于你的开发项目拥有了「记忆」，而不必担心切换开发工具之后，就很难再进行当前的开发进度。只要让 AI 通过阅读文档，就能够清楚地知道当前的项目状况，以及开发进度。

## 原理

与 OpenSpec 类似，SPEC-AGENTS.md 将开发分为不同的阶段，并约定「文档驱动开发」的工作流：先在 `.phrase/phases/` 中用自然语言写好 `spec_*` / `plan_*` / `task_*` 等文档，再由代理按任务执行实现与验证，最后回写 `task_*` / `change_*` / `spec_*` / `issue_*` / `adr_*`，形成可追溯的闭环。

当你在项目中放置 `AGENTS.md` 后，支持该协议的 AI 工具会在会话开始时读取其中规则，把它视为“开发助理使用说明”：  
- 所有需求与接口信息只以文档为准，而不是零散对话；  
- 每次对话只处理一个 `taskNNN` 原子任务；  
- 每个任务完成后，都在变更文档中登记验证方式和影响范围。  

通过这一套约束，SPEC-AGENTS.md 把传统团队里的 spec / task / change / issue 流程，压缩成一个轻量的文本协议，让个人或小团队也能在「氛围编程」状态下享受成熟的软件工程体验。

## 工作流程（How It Works）

下面是 SPEC-AGENTS.md 协议在一个项目中的典型工作流示意图（英文画图，右侧为中文注释）：

```
┌────────────────────┐      起草 / 更新规范（用自然语言写 spec_/plan_）
│ Specs & Plans      │
│ (.phrase/phases/   │
│  spec_/plan_ docs) │
└────────┬───────────┘
         │ define intent & scope                用自然语言描述意图与范围
         ▼
┌────────────────────┐      拆分为原子任务（taskNNN）
│ Atomic taskNNN     │◀──── feedback loop ──────┐  和 AI 来回调整任务边界
└────────┬───────────┘                          │
         │ agreed task boundaries               │ 双方确认每个任务的范围与验收
         ▼                                      │
┌────────────────────┐                          │ 实现与验证（AI 写代码 + 测试）
│ Implement & Verify │──────────────────────────┘
│ (AI writes code &  │
│  runs tests)       │
└────────┬───────────┘
         │ mark taskNNN done                    标记任务完成
         ▼
┌────────────────────┐      归档变更并更新文档
│ Archive & Update   │
│ (change_/spec_/    │
│  issue_/adr_)      │
└────────────────────┘
```

用一句话概括就是：

1. 与 LLM 确定进入新的开发阶段，在 `.phrase/phases/` 里用自然语言起草或更新规范和计划（spec_/plan_）；  
2. 和 AI 一起把需求拆成若干原子 `taskNNN`，来回修改直到双方对范围和验收标准达成一致；  
3. 按照这些文档驱动实现和验证，每次对话聚焦一个任务；  
4. 完成后回写 `task_*` / `change_*` / `spec_*` / `issue_*` / `adr_*`，让所有改动都能从代码追溯回最初的文字意图。

## 实际案例

下面是一个极简的自然对话示例，展示 SPEC-AGENTS.md 在真实会话中的使用方式：

**1. 用户提出需求**

> 用户：  
> 我想给项目加一个「深色模式」开关，用菜单或快捷键切换就行。

**2. AI 回到文档，先锁定任务**

> AI：  
> 好，我先不直接写代码，而是根据 `AGENTS.md` 先更新当前 phase 下的文档：  
> - 在 `.phrase/phases/phase-ui-20250101/plan_ui.md` 补充本次里程碑：支持深色模式切换；  
> - 在 `.phrase/phases/phase-ui-20250101/task_ui.md` 中新增：  
>   - `task003 [ ] 深色模式开关（验证：手动切换 + 界面刷新检查）`。  
> 等你确认这个任务描述和验证方式，我们再进入实现。

**3. 对齐任务边界**

> 用户：  
> 可以，先不做持久化，只要当前会话能来回切换就够了。
>
> AI：  
> 好的，我会把 `task003` 的验证方式改成「手动切换两次，确认 UI 正确刷新」，持久化部分留到后续的 `task004`。

**4. AI 执行实现与验证**

> （AI 内部大致会这样工作）  
> 1. 在代码中查找 UI 层入口（如 `App/` 或 `UI/` 下的窗口或菜单定义）；  
> 2. 按现有风格添加一个「Dark Mode」菜单项或快捷键处理函数；  
> 3. 复用或新增切换主题的核心逻辑（尽量放在 Core/Shared 层）；  
> 4. 运行测试 / 手动启动应用，按 `task003` 的验证方式切换两次，并记录结果。

**5. 回写文档，完成闭环**

> AI：  
> 代码和验证已经完成，我会按照 `AGENTS.md` 的要求回写文档：  
> - 在 `.phrase/phases/phase-ui-20250101/task_ui.md` 中将 `task003` 勾选为 `[x]`；  
> - 在 `.phrase/phases/phase-ui-20250101/change_ui.md` 增加一条记录，说明日期、修改的文件路径、变更类型（Add/Modify）、影响的行为和风险；  
> - 如有必要，补充或更新对应的 `spec_ui.md` 中的「用户操作 → 反馈 → 回退」描述。  
> 之后可以开一个新的 `task004` 专门处理「记住深色模式设置」这一需求。

## 用法

### 使用 Codex
将 SPEC-AGENTS.md 复制到项目文件夹，同时改名为 AGENTS.md。Codex 会自动读取该文件，并遵循该规范。

### 使用 Claude Code
将项目文件夹下的 CLAUDE.md 内容，修改成：

```text
@AGENTS.md
```

### 使用 Gemini CLI 

将项目文件夹下的 GEMINI.md 内容，修改成：

```text
@AGENTS.md
```

