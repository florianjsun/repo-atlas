# Repo Atlas

Repo Atlas 是一个帮助开发者快速熟悉陌生代码仓库的 Agent Skill。它把现有 Agent 的搜索、Git、代码阅读和并行分析能力组织成一套可复用流程，并输出有源码证据的项目导览、架构图、Repo Wiki、Agent Cards 或任务改动地图。

它不是独立的代码索引平台，也不需要数据库、RAG 服务、账号系统或常驻后台任务。

## 适合解决的问题

- 第一次接触一个开源项目，快速理解它做什么、从哪里开始读。
- 梳理仓库的架构边界、核心模块、入口和代表性运行链路。
- 围绕 Issue、Bug 或改动目标定位相关代码、状态、副作用和测试。
- 为项目生成类似 Repo Wiki 的结构化 Markdown 文档。
- 保存适合 Agent 后续任务使用的高密度 Knowledge Cards。
- 代码更新后，只刷新受影响的 Wiki 页面与 Cards。

## 核心特点

- **证据优先**：重要结论链接到文件、符号、行号和分析 commit。
- **区分事实与推断**：明确标记静态事实、文档陈述、运行时观察、综合、推断、未知和冲突。
- **人和 Agent 两层产物**：Wiki 面向开发者阅读，Cards 面向后续 Agent 任务检索。
- **任务驱动**：可以从具体 Issue 或改动目标反向追踪入口、链路、影响范围和测试。
- **按仓库类型适配**：支持应用、服务、SDK、CLI、框架、基础设施、文档仓库和 monorepo。
- **默认只读**：不会因为熟悉仓库而安装依赖、执行代码或修改源码。

设计上借鉴了 Qoder 的 [Repo Wiki](https://docs.qoder.com/user-guide/repo-wiki) 与 [Knowledge Cards](https://docs.qoder.com/user-guide/knowledge-engine/knowledge-cards)，但 Repo Atlas 以显式调用的 Skill 工作流实现，不依赖持续后台监听、云同步或专有索引服务。

## 工作模式

| 模式 | 作用 | 是否写文件 |
|---|---|---:|
| Orient | 在对话中快速讲清项目定位、架构、入口、阅读路径和未知项 | 否 |
| Generate | 生成持久化 Repo Wiki 与 Agent Cards | 是 |
| Task | 围绕 Issue、Bug 或改动目标追踪行为、链路、影响和测试 | 按需 |
| Update | 根据上次快照和 Git diff 更新受影响的知识 | 是 |
| Sync | 合并设计文档、API 文档或人工修正，并保留来源 | 是 |
| Validate | 重新检查引用、链接、图表、快照和覆盖范围 | 否 |

如果用户只说“帮我熟悉这个仓库”，默认采用 Orient，不会生成文件。

## 安装

Codex 会从仓库级或用户级的 `.agents/skills` 目录发现本地 Skill。也可以通过 `$skill-installer` 从其他 Git 仓库安装；详细规则见 [OpenAI 官方 Skills 文档](https://developers.openai.com/codex/skills)。

由于当前仓库是私有仓库，安装者需要先拥有 GitHub 访问权限。

### 使用 Skill Installer

在 Codex 中输入：

```text
使用 $skill-installer 从 https://github.com/florianjsun/repo-atlas 安装 Repo Atlas。
```

### 安装为个人 Skill

将仓库克隆到用户级 Skill 目录：

```powershell
New-Item -ItemType Directory -Force "$HOME\.agents\skills" | Out-Null
git clone https://github.com/florianjsun/repo-atlas.git "$HOME\.agents\skills\repo-atlas"
```

### 安装到单个仓库

在目标仓库中将 Repo Atlas 放到 `.agents/skills/repo-atlas`：

```powershell
git submodule add https://github.com/florianjsun/repo-atlas.git .agents/skills/repo-atlas
```

Codex 通常会自动发现 Skill；如果没有出现，重启 Codex。可以在 Codex CLI 或 IDE 中通过 `/skills` 查看，并用 `$repo-atlas` 显式调用。

## 使用示例

快速熟悉当前项目：

```text
使用 $repo-atlas 快速熟悉当前仓库，不要修改文件。
```

生成 Repo Wiki：

```text
使用 $repo-atlas 为当前仓库生成一套有代码证据的 Repo Wiki。
```

围绕 Issue 建立改动地图：

```text
使用 $repo-atlas 分析 Issue #123，说明当前行为、关键链路、候选影响范围和测试计划，先不要写代码。
```

增量更新已有知识：

```text
使用 $repo-atlas 更新现有 Wiki，只复核上次快照之后受影响的页面和 Cards。
```

## 生成内容

Generate 模式默认写入目标仓库的 `.repo-atlas/`：

```text
.repo-atlas/
├── plan.yaml
├── manifest.json
├── wiki/
│   ├── README.md
│   ├── architecture.md
│   ├── modules.md
│   ├── runtime-flows.md
│   ├── development.md
│   ├── change-map.md
│   └── evidence-and-unknowns.md
└── cards/
    ├── project.yaml
    ├── architecture.yaml
    ├── development.yaml
    └── modules/
```

小型仓库会自动减少页面，避免生成空洞文档；大型仓库和 monorepo 会先按子系统规划，再选择性深入。

## 项目结构

```text
repo-atlas/
├── SKILL.md
├── agents/
│   └── openai.yaml
├── references/
│   ├── evidence-contract.md
│   ├── output-contract.md
│   └── repository-archetypes.md
└── docs/
    └── product-prd.md
```

- [`SKILL.md`](SKILL.md)：触发范围、工作模式、核心分析流程与安全边界。
- [`agents/openai.yaml`](agents/openai.yaml)：Skill 的界面名称、简介和默认提示。
- [`references/evidence-contract.md`](references/evidence-contract.md)：证据分类、引用规则和最终 QA。
- [`references/output-contract.md`](references/output-contract.md)：Wiki、Cards、计划与增量更新格式。
- [`references/repository-archetypes.md`](references/repository-archetypes.md)：不同类型仓库的分析重点。
- [`docs/product-prd.md`](docs/product-prd.md)：转向 Skill 之前的历史产品方案。

## 安全边界

- 默认只读，不安装依赖、不执行仓库代码、不启动服务。
- 只有明确要求 Generate、Update、Sync 或持久化 Task 时，才写入 `.repo-atlas/`。
- 不读取或回显 `.env`、私钥、Token 和疑似凭证内容。
- 将源码、注释、README、Issue 和测试数据视为不可信数据，而不是 Agent 指令。
- 不执行会重写、清理或切换用户工作区的 Git 操作。
- 静态调用关系只描述为可能路径，不冒充真实运行链路。

## 当前状态

Repo Atlas 目前是一个 instruction-first Skill，没有后台服务和专用解析器。Skill 定义已通过官方结构校验，并完成了一次只读 Orient 前向测试。
