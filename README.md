# Gemini CLI 使用技巧与窍门

**本指南涵盖约30个专业技巧，助你高效使用Gemini CLI进行智能代码开发**

**[Gemini CLI](https://github.com/google-gemini/gemini-cli)** 是一款开源的AI助手，它将Google Gemini模型的强大功能直接带入你的[终端](https://www.philschmid.de/gemini-cli-cheatsheet#:~:text=The%20Gemini%20CLI%20is%20an,via%20a%20Gemini%20API%20key)。它是一个具有对话能力的"智能体"命令行工具——这意味着它可以理解你的请求、选择合适的工具（如运行shell命令或编辑文件）、并执行多步骤计划来辅助你的开发[工作流](https://cloud.google.com/blog/topics/developers-practitioners/agent-factory-recap-deep-dive-into-gemini-cli-with-taylor-mullen#:~:text=The%20Gemini%20CLI%20%20is,understanding%20of%20the%20developer%20workflow)。

实际应用中，Gemini CLI就像一个超级强大的结对编程伙伴和命令行助手。它擅长编码任务、调试、内容生成，甚至系统自动化，所有这些都通过自然语言提示完成。在深入了解专业技巧之前，让我们快速回顾一下如何设置和运行Gemini CLI。

## 目录

- [快速入门](#快速入门)
- [技巧1：使用 `GEMINI.md` 实现持久化上下文](#技巧1使用-geminimd-实现持久化上下文)
- [技巧2：创建自定义斜杠命令](#技巧2创建自定义斜杠命令)
- [技巧3：用自己的 `MCP` 服务器扩展Gemini](#技巧3用自己的-mcp-服务器扩展gemini)
- [技巧4：利用记忆添加与回调](#技巧4利用记忆添加与回调)
- [技巧5：使用检查点和 `/restore` 作为撤销按钮](#技巧5使用检查点和-restore-作为撤销按钮)
- [技巧6：读取Google文档、表格等](#技巧6读取google文档表格等配置workspace-mcp服务器后可以粘贴docssheets链接让mcp获取内容需遵守权限规则)
- [技巧7：使用 `@` 引用文件和图像获取显式上下文](#技巧7使用--引用文件和图像获取显式上下文)
- [技巧8：即时创建工具（让Gemini构建辅助工具）](#技巧8即时创建工具让gemini构建辅助工具)
- [技巧9：使用Gemini CLI进行系统故障排查和配置](#技巧9使用gemini-cli进行系统故障排查和配置)
- [技巧10：YOLO模式 - 自动批准工具操作（谨慎使用）](#技巧10yolo模式---自动批准工具操作谨慎使用)
- [技巧11：无头模式和脚本模式（在后台运行Gemini CLI）](#技巧11无头模式和脚本模式在后台运行gemini-cli)
- [技巧12：保存和恢复聊天会话](#技巧12保存和恢复聊天会话)
- [技巧13：多目录工作区 - 一个Gemini管理多个文件夹](#技巧13多目录工作区---一个gemini管理多个文件夹)
- [技巧14：借助AI协助整理和清理文件](#技巧14借助ai协助整理和清理文件)
- [技巧15：压缩长对话以保持在上下文范围内](#技巧15压缩长对话以保持在上下文范围内)
- [技巧16：使用 `!` 直通Shell命令（与终端对话）](#技巧16使用--直通shell命令与终端对话)
- [技巧17：将每个CLI工具视为潜在的Gemini工具](#技巧17将每个cli工具视为潜在的gemini工具)
- [技巧18：利用多模态AI - 让Gemini看见图像等内容](#技巧18利用多模态ai---让gemini看见图像等内容)
- [技巧19：自定义 `$PATH`（以及工具可用性）以提高稳定性](#技巧19自定义-path以及工具可用性以提高稳定性)
- [技巧20：使用令牌缓存和统计跟踪并降低令牌消耗](#技巧20使用令牌缓存和统计跟踪并降低令牌消耗)
- [技巧21：使用 `/copy` 快速复制到剪贴板](#技巧21使用-copy-快速复制到剪贴板)
- [技巧22：掌握 `Ctrl+C` 用于Shell模式和退出](#技巧22掌握-ctrlc-用于shell模式和退出)
- [技巧23：使用 `settings.json` 自定义Gemini CLI](#技巧23使用-settingsjson-自定义gemini-cli)
- [技巧24：利用IDE集成（VS Code）获取上下文和差异](#技巧24利用ide集成vs-code获取上下文和差异)
- [技巧25：使用 `Gemini CLI GitHub Action` 自动化仓库任务](#技巧25使用-gemini-cli-github-action-自动化仓库任务)
- [技巧26：启用遥测以获取洞察和可观测性](#技巧26启用遥测以获取洞察和可观测性)
- [技巧27：关注路线图（后台智能体等功能）](#技巧27关注路线图后台智能体等功能)
- [技巧28：使用 `扩展` 扩展Gemini CLI](#技巧28使用-扩展-扩展gemini-cli)
- [技巧29：柯基模式彩蛋 🐕](#附加趣味柯基模式彩蛋-)

## 快速入门

**安装：** 你可以通过npm安装Gemini CLI。全局安装使用：

```bash
npm install -g @google/gemini-cli
```

或者使用 `npx` 运行而无需安装：

```bash
npx @google/gemini-cli
```

Gemini CLI可在所有主流平台上使用（它是用Node.js/TypeScript构建的）。安装后，只需在终端中运行 `gemini` 命令即可启动交互式[CLI](https://www.philschmid.de/gemini-cli-cheatsheet#:~:text=Interactive%20Mode%20,conversational%20session)。

**身份验证：** 首次使用时，你需要通过Gemini服务进行身份验证。你有两个选项：(1) **Google账户登录（免费套餐）** - 这允许你免费使用Gemini 2.5 Pro，并享有慷慨的使用限制（约每分钟60个请求，每[天](https://blog.google/technology/developers/introducing-gemini-cli-open-source-ai-agent/#:~:text=Unmatched%20usage%20limits%20for%20individual,developers)1000个请求）。启动时，Gemini CLI会提示你使用Google账户登录（无需[计费](https://genmind.ch/posts/Howto-Supercharge-Your-Terminal-with-Gemini-CLI/#:~:text=%2A%20Google,Google%20AI%20Studio%2C%20then%20run)）。(2) **API密钥（付费或更高级别访问）** - 你可以从Google AI [Studio](https://www.philschmid.de/gemini-cli-cheatsheet#:~:text=1,key%20from%20Google%20AI%20Studio)获取API密钥，并设置环境变量 `GEMINI_API_KEY` 来使用[它](https://www.philschmid.de/gemini-cli-cheatsheet#:~:text=Method%201%3A%20Shell%20Environment%20Variable,zshrc)。

API密钥使用可以提供更高的配额和企业数据使用保护；在付费/计费使用中，提示不会用于训练，但日志可能会为了[安全](https://genmind.ch/posts/Howto-Supercharge-Your-Terminal-with-Gemini-CLI/#:~:text=responses%20may%20be%20logged%20for,Google%20AI%20Studio%2C%20then%20run)而保留。

例如，添加到你的shell配置文件：

```bash
export GEMINI_API_KEY="YOUR_KEY_HERE"
```

**基本用法：** 要启动交互式会话，只需运行不带参数的 `gemini`。你会看到一个 `gemini>` 提示符，你可以在此输入请求或命令。例如：

```bash
$ gemini
gemini> 使用SQLite创建一个React配方管理应用
```

然后你可以观看Gemini CLI创建文件、安装依赖、运行测试等，来满足你的请求。如果你喜欢一次性调用（非交互式），使用 `-p` 标志和提示，例如：

```bash
gemini -p "总结附加文件的要点。@./report.txt"
```

这将输出单个响应并[退出](https://www.philschmid.de/gemini-cli-cheatsheet#:~:text=gemini)。你还可以将输入通过管道传递给Gemini CLI：例如，`echo "数到10" | gemini` 将通过[stdin](https://www.philschmid.de/gemini-cli-cheatsheet#:~:text=gemini%20,txt)提供提示。

**CLI界面：** Gemini CLI提供了类似REPL的丰富界面。它支持**斜杠命令**（以 `/` 为前缀的特殊命令，用于控制会话、工具和设置）和**感叹号命令**（以 `!` 为前缀直接执行shell命令）。我们将在下面的专业技巧中介绍其中许多。默认情况下，Gemini CLI在安全模式下运行，任何修改系统的操作（写文件、运行shell命令等）都会要求确认。当提出工具操作时，你会看到差异或命令，并被提示（`Y/n`）批准或拒绝它。这确保AI不会在未经你同意的情况下进行不需要的更改。

基础知识已经介绍完毕，现在让我们探索一系列专业技巧和隐藏功能，帮助你充分利用Gemini CLI。每个技巧都先给出一个简单的示例，然后深入介绍细节和细微差别。这些技巧融合了工具创建者（如Taylor Mullen）和Google开发者关系团队以及更广泛社区的建议和见解，作为Gemini CLI高级用户的**权威指南**。

## 技巧1：使用 `GEMINI.md` 实现持久化上下文

**快速用例：** 停止在提示中重复自己。通过创建 `GEMINI.md` 文件提供项目特定的上下文或说明，这样AI始终拥有重要的背景知识，而无需每次都告诉[它](https://www.philschmid.de/gemini-cli-cheatsheet#:~:text=Context%20Files%20%28)。

在项目上工作时，你通常有某些总体细节——例如编码风格指南、项目架构或重要事实——你希望AI记住。Gemini CLI允许你将这些编码到一个或多个 `GEMINI.md` 文件中。只需在项目中创建一个 `.gemini` 文件夹（如果尚不存在），并添加一个名为 `GEMINI.md` 的Markdown文件，其中包含你希望AI保留的任何注释或说明。例如：

```markdown
# Project Phoenix - AI助手

- 所有Python代码必须遵循PEP 8风格。  
- 使用4个空格缩进。  
- 用户正在构建数据管道；优先使用函数式编程范式。
```

将此文件放在项目根目录中（或子目录中以获取更细粒度的上下文）。现在，每当你在该项目中运行 `gemini` 时，它将自动将这些说明加载到[上下文](https://www.philschmid.de/gemini-cli-cheatsheet#:~:text=Context%20Files%20%28)中。这意味着模型将*始终*准备好这些内容，避免了在每个提示前添加相同指导的需要。

**工作原理：** Gemini CLI使用分层上下文加载[系统](https://www.philschmid.de/gemini-cli-cheatsheet#:~:text=Hierarchical%20Loading%3A%20The%20CLI%20combines,The%20loading%20order%20is)。它会将**全局上下文**（来自 `~/.gemini/GEMINI.md`，你可以将其用于跨项目默认设置）与你的**项目特定 `GEMINI.md`** 结合，甚至包括子文件夹中的上下文文件。更具体的文件会覆盖更通用的文件。你可以随时使用以下命令检查加载了什么上下文：

```bash
/memory show
```

这将显示AI看到的完整组合[上下文](https://www.philschmid.de/gemini-cli-cheatsheet#:~:text=,current%20conversation%20with%20a%20tag)。如果你对 `GEMINI.md` 进行了更改，使用 `/memory refresh` 可以在不重启[会话](https://www.philschmid.de/gemini-cli-cheatsheet#:~:text=,current%20conversation%20with%20a%20tag)的情况下重新加载上下文。

**专业技巧：** 使用 `/init` 斜杠命令快速生成起始 `GEMINI.md`。在新项目中运行 `/init` 会创建一个模板上下文文件，其中包含检测到的技术栈、项目摘要[等](https://www.philschmid.de/gemini-cli-cheatsheet#:~:text=,directory%20workspace%20%28e.g.%2C%20%60add)信息。然后你可以编辑和扩展该文件。对于大型项目，考虑将上下文分成多个文件，并使用 `@include` 语法将它们**导入**到 `GEMINI.md` 中。例如，你的主 `GEMINI.md` 可以有类似 `@./docs/prompt-guidelines.md` 的行来引入额外的上下文[文件](https://www.philschmid.de/gemini-cli-cheatsheet#:~:text=Modularizing%20Context%20with%20Imports%3A%20You,files)。这样可以保持你的说明有序。

通过精心设计的 `GEMINI.md`，你实际上给了Gemini CLI项目需求和约定的"记忆"。这种**持久化上下文**能带来更相关的响应，减少来回的提示工程。

## 技巧2：创建自定义斜杠命令

**快速用例：** 通过定义自己的斜杠命令来加速重复任务。例如，你可以创建一个 `/test:gen` 命令，从描述生成单元测试，或者 `/db:reset` 来删除并重新创建测试数据库。这通过针对你工作流程定制的单行命令扩展了Gemini CLI的功能。

Gemini CLI支持**自定义斜杠命令**，你可以在简单的配置文件中定义它们。在底层，这些本质上是预定义的提示模板。要创建一个，在 `~/.gemini/` 下创建一个 `commands/` 目录用于全局命令，或在项目的 `.gemini/` 文件夹中创建用于项目特定[命令](https://www.philschmid.de/gemini-cli-cheatsheet#:~:text=Custom%20Commands)。在 `commands/` 内，为每个新命令创建一个TOML文件。文件名格式决定命令名称：例如，文件 `test/gen.toml` 定义命令 `/test:gen`。

让我们通过一个例子来演示。假设你想要一个从需求描述生成单元测试的命令。你可以创建 `~/.gemini/commands/test/gen.toml`，内容如下：

```markdown
# 调用方式：/test:gen "测试描述"  
description \= "根据需求生成单元测试。"  
prompt \= """  
你是一位专家测试工程师。基于以下需求，请使用Jest框架编写一个全面的单元测试。

需求：{{args}}  
"""
```

现在，在重新加载或重启Gemini CLI后，你只需输入：

```bash
/test:gen "确保登录按钮在成功时重定向到仪表板"
```

Gemini CLI将识别 `/test:gen` 并用提供的参数（在本例中是需求）替换提示模板中的 `{{args}}`。然后AI将相应地生成Jest单元[测试](https://www.philschmid.de/gemini-cli-cheatsheet#:~:text=Example%3A%20%60)。`description` 字段是可选的，但在你运行 `/help` 或 `/tools` 列出可用命令时会使用。

这个机制非常强大——实际上，你可以用自然语言编写AI脚本。社区创建了许多有用的自定义命令。例如，Google的DevRel团队分享了一组*10个实用工作流命令*（通过开源仓库），演示了如何编写常见流程脚本，如创建API文档、清理数据或设置样板[代码](https://cloud.google.com/blog/topics/developers-practitioners/agent-factory-recap-deep-dive-into-gemini-cli-with-taylor-mullen#:~:text=,to%20generate%20a%20better%20output)。通过定义自定义命令，你将复杂的提示（或一系列提示）打包成可重用的快捷方式。

**专业技巧：** 自定义命令也可以用来强制格式化或为某些任务应用AI的"角色"。例如，你可能有一个 `/review:security` 命令，它总是在提示前加上"你是一位安全审计员..."来审查代码中的漏洞。这种方法确保AI对特定类别任务的响应保持一致性。

要与团队共享命令，你可以在项目仓库中提交TOML文件（在 `.gemini/commands` 目录下）。拥有Gemini CLI的团队成员在项目中工作时将自动获取这些命令。这是在团队中**标准化AI辅助工作流程**的好方法。

## 技巧3：用自己的 `MCP` 服务器扩展Gemini

**快速用例：** 假设你希望Gemini与外部系统或不是内置的自定义工具交互——例如，查询专有数据库，或与Figma设计集成。你可以通过运行自定义**模型上下文协议（MCP）服务器**并将其插入Gemini [CLI](https://www.philschmid.de/gemini-cli-cheatsheet#:~:text=Extend%20the%20CLI%20with%20your,add%7Clist%7Cremove%3E%60%20commands)来实现。MCP服务器让你可以向Gemini添加新工具和能力，有效地**扩展智能体**。

Gemini CLI自带几个开箱即用的MCP服务器（例如，支持Google搜索、代码执行沙箱等的服务器），你也可以添加自己的。MCP服务器本质上是一个外部进程（可以是本地脚本、微服务，甚至是云端点），它使用简单的协议来处理Gemini的任务。这种架构使Gemini CLI如此[可扩展](https://blog.google/technology/developers/introducing-gemini-cli-open-source-ai-agent/#:~:text=,interactively%20within%20your%20scripts)。

**MCP服务器示例：** 一些社区和Google提供的MCP集成包括**Figma MCP**（从Figma获取设计细节）、**剪贴板MCP**（从系统剪贴板读取/写入）等。实际上，在一次内部演示中，Gemini CLI团队展示了一个"Google Docs MCP"服务器，允许直接将内容保存到Google [Docs](https://cloud.google.com/blog/topics/developers-practitioners/agent-factory-recap-deep-dive-into-gemini-cli-with-taylor-mullen#:~:text=%2A%20Utilize%20the%20google,summary%20directly%20to%20Google%20Docs)。其思想是，每当Gemini需要执行内置工具无法处理的操作时，它可以委托给你的MCP服务器。

**如何添加：** 你可以通过 `settings.json` 或使用CLI配置MCP服务器。快速设置，尝试CLI命令：

```bash
gemini mcp add myserver --command "python3 my_mcp_server.py" --port 8080
```

这将注册一个名为"myserver"的服务器，Gemini CLI将通过运行给定的命令（这里是Python模块）在端口8080上启动它。在 `~/.gemini/settings.json` 中，它会在 `mcpServers` 下添加一个条目。例如：

```json
"mcpServers": {
  "myserver": {
    "command": "python3",
    "args": ["-m", "my_mcp_server", "--port", "8080"],
    "cwd": "./mcp_tools/python",
    "timeout": 15000
  }
}
```

此配置（基于官方文档）告诉Gemini如何启动MCP服务器以及在[哪里](https://www.philschmid.de/gemini-cli-cheatsheet#:~:text=Example%20)。运行后，该服务器提供的工具将可供Gemini CLI使用。你可以使用斜杠命令列出所有MCP服务器及其工具：

```bash
/mcp
```

这将显示任何已注册的服务器及其公开的工具[名称](https://www.philschmid.de/gemini-cli-cheatsheet#:~:text=Command%20Description%20,List%20active%20extensions)。

**MCP的威力：** MCP服务器可以提供**丰富的多模态结果**。例如，通过MCP提供的工具可以返回图像或格式化表格作为对Gemini [CLI](https://www.philschmid.de/gemini-cli-cheatsheet#:~:text=Capabilities%3A)响应的一部分。它们还支持OAuth 2.0，因此你可以通过MCP工具安全地连接到API（如Google的API、GitHub等），而无需暴露[凭据](https://www.philschmid.de/gemini-cli-cheatsheet#:~:text=Extend%20the%20CLI%20with%20your,add%7Clist%7Cremove%3E%60%20commands)。本质上，如果你可以编码它，就可以将其包装为MCP工具——将Gemini CLI变成编排许多服务的中心。

**默认vs自定义：** 默认情况下，Gemini CLI的内置工具涵盖很多功能（读取文件、网络搜索、执行shell命令等），但MCP让你可以走得更远。一些高级用户创建了MCP服务器来与内部系统交互或执行专门的数据处理。例如，你可以有一个 `database-mcp`，提供 `/query_db` 工具在公司数据库上运行SQL查询，或者 `jira-mcp` 通过自然语言创建工单。

创建自己的MCP时，要注意安全性：默认情况下，自定义MCP工具需要确认，除非你将它们标记为受信任。你可以通过设置控制安全性，如为服务器设置 `trust: true`（自动批准其工具操作）或通过白名单列出特定的安全工具和黑名单列出危险[工具](https://www.philschmid.de/gemini-cli-cheatsheet#:~:text=,takes%20precedence)。

简而言之，**MCP服务器解锁了无限的集成可能性**。它们是一个专业功能，让Gemini CLI成为你的AI助手与你需要它协作的任何系统之间的粘合剂。如果你有兴趣构建一个，请查看官方[MCP指南](https://www.philschmid.de/gemini-cli-cheatsheet#:~:text=Transport%20)和社区示例。

## 技巧4：利用记忆添加与回调

**快速用例：** 通过将重要事实添加到AI的长期记忆中，让它们随时可用。例如，在找出数据库端口或API令牌后，你可以执行：

```bash
/memory add "我们的staging RabbitMQ在端口5673上"
```

这将存储该事实，这样你（或AI）以后不会忘记[它](https://binaryverseai.com/gemini-cli-open-source-ai-tool/#:~:text=Gemini%20CLI%20Ultimate%20Agent%3A%2060,a%20branch%20of%20conversation)。然后你可以随时使用 `/memory show` 回调记忆中的所有内容。

`/memory` 命令提供了一个简单但强大的*持久记忆*机制。当你使用 `/memory add <文本>` 时，给定的文本会被附加到你项目的全局上下文中（技术上，它被保存到全局 `~/.gemini/GEMINI.md` 文件或项目的[`GEMINI.md`](https://genmind.ch/posts/Howto-Supercharge-Your-Terminal-with-Gemini-CLI/#:~:text=,load%20memory%20from%20%60GEMINI.md)中）。这有点像做笔记并将其固定在AI的虚拟公告板上。添加后，AI将始终在未来交互的提示上下文中看到该注释，跨会话。

考虑一个例子：你正在调试一个问题，发现了一个不明显的见解（"配置标志 `X_ENABLE` 必须设置为 `true`，否则服务无法启动"）。如果你将此添加到记忆中，以后如果你或AI讨论相关问题时，它不会忽略这个关键细节——它在上下文中。

**使用 `/memory`：**

* `/memory add "<文本>"` - 向记忆添加事实或注释（持久上下文）。这会立即用新条目更新 `GEMINI.md`。

* `/memory show` - 显示记忆的完整内容（即当前加载的组合上下文文件）。

* `/memory refresh` - 从磁盘重新加载上下文（如果你在Gemini CLI之外手动编辑了 `GEMINI.md` 文件，或者多人正在协作，这很有用）。

因为记忆存储在Markdown中，你也可以手动编辑 `GEMINI.md` 文件来整理或组织信息。`/memory` 命令是为了在对话期间方便使用，这样你就不必打开编辑器。

**专业技巧：** 这个功能非常适合"决策日志"。如果你在聊天期间决定了某种方法或规则（例如，使用某个库，或约定的代码风格），请将其添加到记忆中。然后AI会回忆起该决定，以后不会与之矛盾。在可能持续数小时或数天的长会话中特别有用——通过保存关键点，你可以缓解模型在对话变长时忘记早期上下文的倾向。

另一个用途是个人笔记。因为 `~/.gemini/GEMINI.md`（全局记忆）对所有会话加载，你可以在那里放置一般偏好或信息。例如，"用户的名字是Alice。礼貌地说话，避免俚语。"这就像配置AI的角色或全局知识。只是要注意全局记忆适用于*所有*项目，所以不要用项目特定的信息杂乱它。

总之，**记忆添加与回调**帮助Gemini CLI维护状态。将其视为随项目增长的知识库。使用它来避免重复自己或提醒AI它否则必须从头重新发现的事实。

## 技巧5：使用检查点和 `/restore` 作为撤销按钮

**快速用例：** 如果Gemini CLI对你的文件进行了一系列你不满意的更改，你可以*立即回滚*到之前的状态。在启动Gemini时启用检查点（或在设置中），并使用 `/restore` 命令撤销更改，就像轻量级的Git [revert](https://www.philschmid.de/gemini-cli-cheatsheet#:~:text=,Exit%20the%20Gemini%20CLI)。`/restore` 将你的工作区回滚到保存的检查点；对话状态可能会受到影响，具体取决于检查点的捕获方式。

Gemini CLI的**检查点**功能充当安全网。启用后，CLI在每次修改[文件](https://www.philschmid.de/gemini-cli-cheatsheet#:~:text=When%20,snapshot%20before%20tools%20modify%20files)的工具执行*之前*会对项目文件进行快照。如果出了问题，你可以恢复到最后已知的良好状态。它本质上是AI操作的版本控制，无需你每次手动提交到Git。

**如何使用：** 你可以通过使用 `--checkpointing` 标志启动CLI来打开检查点：

```bash
gemini --checkpointing
```

或者，你可以通过在配置中添加（在[`settings.json`](https://www.philschmid.de/gemini-cli-cheatsheet#:~:text=%7B%20,true)中使用 `"checkpointing": { "enabled": true }`）将其设为默认值。激活后，你会注意到每次Gemini准备写入文件时，它会说类似"检查点已保存"的内容。

如果你随后意识到AI所做的编辑有问题，你有两个选项：

* 运行 `/restore list`（或不带参数的 `/restore`）查看带有时间戳和描述的最近检查点列表。

* 运行 `/restore <id>` 回滚到特定检查点。如果你省略id且只有一个待处理的检查点，它将默认恢复[那个](https://medium.com/@ferreradaniel/gemini-cli-free-ai-tool-upgrade-5-new-features-you-need-right-now-04cfefac5e93#:~:text=Step)。

例如：

```bash
/restore
```

Gemini CLI可能输出：

0: \[2025-09-22 10:30:15\] Before running 'apply_patch'  
1: \[2025-09-22 10:45:02\] Before running 'write_file'

然后你可以执行 `/restore 0` 将所有文件更改（甚至对话上下文）恢复到该检查点时的状态。通过这种方式，你可以"撤销"错误的代码重构或Gemini [做出](https://medium.com/@ferreradaniel/gemini-cli-free-ai-tool-upgrade-5-new-features-you-need-right-now-04cfefac5e93#:~:text=1,point%20and%20roll%20back%20instantly)的任何其他更改。

**恢复的内容：** 检查点捕获你的工作目录状态（Gemini CLI被允许修改的所有文件）和工作区文件（对话状态也可能被回滚，具体取决于检查点的捕获方式）。恢复时，它会将文件覆盖到旧版本并将对话记忆重置到该快照。就像让AI智能体时间旅行回到走错路之前。请注意，它不会撤销外部副作用（例如，如果AI运行了数据库迁移，它无法撤销），但文件系统和聊天上下文中的任何内容都可以撤销。

**最佳实践：** 对于非平凡的任务，保持检查点打开是个好主意。开销很小，并提供安心。如果你发现不需要检查点（一切顺利），你总是可以清除它或让下一个覆盖它。开发团队建议特别在多步骤代码[编辑](https://medium.com/@ferreradaniel/gemini-cli-free-ai-tool-upgrade-5-new-features-you-need-right-now-04cfefac5e93#:~:text=Tips%20to%20avoid%20messy%20rollbacks)之前使用检查点。不过，对于关键任务项目，你仍应使用适当的版本控制（`git`）作为主要安全[网](https://medium.com/@ferreradaniel/gemini-cli-free-ai-tool-upgrade-5-new-features-you-need-right-now-04cfefac5e93#:~:text=No,VS%20Code%20is%20already%20free)——将检查点视为快速撤销的便利，而不是完整的VCS。

本质上，`/restore` 让你可以放心地使用Gemini CLI。你可以让AI尝试大胆的更改，知道如果需要，你有一个*"哦不"按钮*来倒带。

## 技巧6：读取Google文档、表格等。配置Workspace MCP服务器后，可以粘贴Docs/Sheets链接，让MCP获取内容（需遵守权限规则）

**快速用例：** 假设你有一个包含一些规格或数据的Google文档或表格，你希望AI使用它。你无需复制粘贴内容，只需提供链接，配置好Workspace MCP服务器后，Gemini CLI就能获取并读取它。

例如：

```bash
总结这个设计文档中的需求：https://docs.google.com/document/d/<id>
```

Gemini可以拉取该文档的内容并将其纳入响应中。同样，它可以通过链接读取Google Sheets或Drive文件。

**工作原理：** 这些功能通常通过**MCP集成**实现。Google的Gemini CLI团队已经构建（或正在开发）Google Workspace的连接器。一种方法是运行一个小型MCP服务器，当给定URL或[ID](https://github.com/google-gemini/gemini-cli/issues/7175)时，使用Google的API（Docs API、Sheets API等）检索文档内容。配置后，你可能会有斜杠命令或工具，如 `/read_google_doc`，或者简单的自动检测，看到Google Docs链接并调用适当的工具来获取它。

例如，在Agent Factory播客演示中，团队使用**Google Docs MCP**将摘要直接保存到[文档](https://cloud.google.com/blog/topics/developers-practitioners/agent-factory-recap-deep-dive-into-gemini-cli-with-taylor-mullen#:~:text=%2A%20Utilize%20the%20google,summary%20directly%20to%20Google%20Docs)中——这意味着他们也可以首先读取文档的内容。实际上，你可能会这样做：

```bash
@https://docs.google.com/document/d/XYZ12345
```

在URL前加上 `@`（上下文引用语法）会向Gemini CLI发出获取该资源的信号。配置好Google Doc集成后，该文档的内容将被拉取进来，就像它是本地文件一样。从那里，AI可以总结它、回答关于它的问题，或以其他方式在对话中使用它。

类似地，如果你粘贴Google Drive**文件链接**，正确配置的Drive工具可以下载或打开该文件（假设设置了权限和API访问）。**Google Sheets**可以通过运行查询或读取单元格范围的MCP提供，使你可以询问诸如"此表格\[链接\]中预算列的总和是多少？"之类的问题，并让AI计算它。

**设置方法：** 截至撰写本文时，Google Workspace集成可能需要一些调整（获取API凭据，运行MCP服务器，如[Kanshi Tanaike](https://medium.com/google-cloud/managing-google-docs-sheets-and-slides-by-natural-language-with-gemini-cli-and-mcp-62f4dfbef2d5#:~:text=To%20implement%20this%20approach%2C%20I,methods%20for%20each%20respective%20API)所描述的等）。关注官方Gemini CLI仓库和社区论坛，寻找即用型扩展——例如，官方Google Docs MCP可能作为插件/扩展提供。如果你很急切，你可以按照如何在MCP [服务器](https://github.com/google-gemini/gemini-cli/issues/7175#:~:text=)中使用Google API的指南编写一个。它通常涉及处理OAuth（Gemini CLI支持MCP服务器的OAuth），然后公开像 `read_google_doc` 这样的工具。

**使用提示：** 当你拥有这些工具时，使用它们可以简单到在提示中提供链接（AI可能会自动调用工具来获取它），或使用斜杠命令如 `/doc open <URL>`。检查 `/tools` 以查看哪些命令可用——Gemini CLI在[那里](https://dev.to/therealmrmumba/7-insane-gemini-cli-tips-that-will-make-you-a-superhuman-developer-2d7h#:~:text=Gemini%20CLI%20includes%20dozens%20of,can%20supercharge%20your%20dev%20process)列出所有工具和自定义命令。

总之，**Gemini CLI可以超越你的本地文件系统**。无论是Google Docs、Sheets、Drive还是其他外部内容，你都可以通过引用提取数据。这个专业技巧省去了手动复制粘贴的麻烦，保持上下文流程自然——只需引用你需要的文档或数据集，让AI抓取所需内容。这使Gemini CLI成为你有权访问的所有信息的真正**知识助手**，而不仅仅是磁盘上的文件。

*（注意：访问私有文档当然需要CLI具有适当的权限。始终确保任何集成尊重安全和隐私。在企业环境中，设置此类集成可能涉及额外的身份验证步骤。）*

## 技巧7：使用 `@` 引用文件和图像获取显式上下文

**快速用例：** 不用口头描述文件内容或图像，只需直接将Gemini CLI指向它即可。使用 `@` 语法，你可以将文件、目录或图像附加到提示中。这保证AI能准确看到这些文件中的内容作为[上下文](https://www.philschmid.de/gemini-cli-cheatsheet#:~:text=Reference%20files%20or%20directories%20in,PDFs%2C%20audio%2C%20and%20video%20files)。例如：

```bash
向我解释这段代码：@./src/main.js
```

这将在提示中包含 `src/main.js` 的内容（在Gemini的上下文大小限制内），这样AI就可以阅读并解释[它](https://www.philschmid.de/gemini-cli-cheatsheet#:~:text=Include%20a%20single%20file%3A)。

这个 `@` *文件引用*是Gemini CLI为开发者提供的最强大功能之一。它消除了歧义——你不是要求模型依赖记忆或对文件的猜测，而是真正地把文件交给它阅读。你可以将此用于源代码、文本文档、日志等。类似地，你可以引用**整个目录**：

```bash
重构 @./utils/ 中的代码以使用async/await。
```

通过附加以斜杠结尾的路径，Gemini CLI将递归地包含该[目录](https://www.philschmid.de/gemini-cli-cheatsheet#:~:text=Include%20a%20whole%20directory%20)中的文件（在合理范围内，尊重忽略文件和大小限制）。这对于多文件重构或分析非常有用，因为AI可以一起考虑所有相关模块。

更令人印象深刻的是，你可以在提示中引用**二进制文件如图像**。Gemini CLI（使用Gemini模型的多模态功能）可以理解图像。例如：

```bash
描述你在这个截图中看到的内容：@./design/mockup.png
```

图像将被输入到模型中，AI可能会回应类似"这是一个带有蓝色登录按钮和标题图像的登录页面"[等](https://www.philschmid.de/gemini-cli-cheatsheet#:~:text=Include%20an%20image%3A)。你可以想象其用途：审查UI模型、整理照片（正如我们将在后面的技巧中看到的），或从图像中提取文本（Gemini也可以进行OCR）。

有效使用 `@` 引用的几点注意事项：

* **文件限制：** Gemini 2.5 Pro拥有巨大的上下文窗口（最多100万个[令牌](https://blog.google/technology/developers/introducing-gemini-cli-open-source-ai-agent/#:~:text=To%20use%20Gemini%20CLI%20free,per%20day%20at%20no%20charge)），因此你可以包含相当大的文件或许多文件。但是，极大的文件可能会被截断。如果文件巨大（比如说，数十万行），考虑总结它或将其分成几部分。如果引用太大或由于大小而跳过了某些内容，Gemini CLI会警告你。

* **自动忽略：** 默认情况下，Gemini CLI在拉取目录[上下文](https://www.philschmid.de/gemini-cli-cheatsheet#:~:text=Reference%20files%20or%20directories%20in,PDFs%2C%20audio%2C%20and%20video%20files)时会尊重你的 `.gitignore` 和 `.geminiignore` 文件。因此，如果你 `@./` 一个项目根目录，它不会将巨大的被忽略文件夹（如 `node_modules`）转储到提示中。你可以使用 `.geminiignore` 自定义忽略模式，类似于 `.gitignore` 的工作方式。

* **显式vs隐式上下文：** Taylor Mullen（Gemini CLI的创建者）强调使用 `@` 进行*显式上下文注入*，而不是依赖模型的记忆或自己总结内容。这更精确，确保AI不会幻想内容。尽可能使用 `@` 引用将AI指向真相来源（代码、配置文件、文档）。这种做法可以显著提高准确性。

* **链接引用：** 你可以在一个提示中包含多个文件，例如：

```bash
比较 @./foo.py 和 @./bar.py 并告诉我差异。
```

CLI将包含两个文件。只需注意令牌限制；多个大文件可能会消耗大量上下文窗口。

使用 `@` 本质上是你**即时向Gemini CLI提供知识**的方式。它将CLI变成一个可以处理文本和图像的多模态阅读器。作为高级用户，养成利用这一点的习惯——它通常比询问AI诸如"打开文件X并执行Y"之类的事情更快更可靠（它可能做也可能不做）。相反，你明确地给它X来工作。

## 技巧8：即时创建工具（让Gemini构建辅助工具）

**快速用例：** 如果手头的任务受益于小脚本或实用程序，你可以要求Gemini CLI为你创建该工具——就在你的会话中。例如，你可能会说，"编写一个Python脚本来解析此文件夹中的所有JSON文件并提取错误字段。"Gemini可以生成脚本，然后你可以通过CLI执行它。本质上，你可以在进行中**动态扩展工具集**。

Gemini CLI不限于其预先存在的工具；在需要时，它可以使用其编码能力制造新工具。这通常隐式发生：如果你要求复杂的东西，AI可能会提议编写一个临时文件（带代码）然后运行它。作为用户，你也可以明确地指导这个过程：

* **创建脚本：** 你可以提示Gemini创建你选择的语言的脚本或程序。它可能会使用 `write_file` 工具创建文件。例如：

```bash
生成一个Node.js脚本，读取当前目录中所有'.log'文件并报告每个文件的行数。
```

Gemini CLI将起草代码，并在你批准后将其写入文件（例如 `script.js`）。然后你可以通过使用 `!` shell命令（例如 `!node script.js`）运行它，或者要求Gemini CLI执行它（如果AI认为它是计划的一部分，可能会自动使用 `run_shell_command` 执行它刚刚编写的脚本）。

* **通过MCP的临时工具：** 在高级场景中，AI甚至可能建议为某些专门任务启动MCP服务器。例如，如果你的提示涉及一些可能用Python更好完成的重文本处理，Gemini可以在Python中生成一个简单的MCP服务器并运行它。虽然这种情况较少见，但它表明AI可以即时设置新的"智能体"。（Gemini CLI团队的一张幻灯片幽默地提到"所有事物的MCP服务器，甚至一个叫LROwn的"——暗示你可以让Gemini运行自己或另一个模型的实例，尽管那更多是个技巧而非实用！）

这里的关键好处是**自动化**。你无需手动停下来编写辅助脚本，可以让AI作为流程的一部分来完成它。这就像有一个可以按需创建工具的助手。这对于数据转换任务、批量操作或内置工具不直接提供的一次性计算特别有用。

**细微差别和安全性：** 当Gemini CLI为新工具编写代码时，你仍应在运行之前审查它。`/diff` 视图（Gemini会在你批准写入之前显示文件差异）是你检查[代码](https://medium.com/@ferreradaniel/gemini-cli-free-ai-tool-upgrade-5-new-features-you-need-right-now-04cfefac5e93#:~:text=Nobody%20enjoys%20switching%20between%20windows,track%20changes%20line%20by%20line)的机会。确保它按你预期的方式工作，没有恶意或破坏性内容（AI不应产生有害的东西，除非你的提示明确要求，但就像任何来自AI的代码一样，仔细检查逻辑，特别是对于删除或修改大量数据的脚本）。

**示例场景：** 假设你有一个CSV文件，想以复杂的方式过滤它。你要求Gemini CLI这样做，它可能会说："我将编写一个Python脚本来解析CSV并应用过滤器。"然后它创建 `filter_data.py`。在你批准并运行后，你得到结果，可能再也不需要那个脚本了。这种工具的临时创建是一个专业技巧——它显示AI有效地自主扩展其能力。

**专业技巧：** 如果你发现脚本在即时上下文之外有用，可以将其提升为永久工具或命令。例如，如果AI生成了一个很棒的日志处理脚本，你以后可能会将其转换为自定义斜杠命令（技巧#2）以便于重用。Gemini的生成能力和扩展钩子的结合意味着你的工具包可以随着你使用CLI不断发展。

总之，**不要限制Gemini只能使用它自带的东西**。将它视为一个初级开发者，可以快速编写新程序甚至迷你服务器来帮助解决问题。这种方法体现了Gemini CLI的智能体哲学——它会弄清楚需要什么工具，即使必须当场编码它们。

## 技巧9：使用Gemini CLI进行系统故障排查和配置

**快速用例：** 你可以在代码项目之外运行Gemini CLI来帮助完成一般系统任务——将其视为操作系统的智能助手。例如，如果你的shell出现故障，你可以在主目录中打开Gemini并询问："修复我的 `.bashrc` 文件，它有错误。"Gemini然后可以为你打开并编辑配置文件。

这个技巧强调**Gemini CLI不仅用于编码项目——它是你整个开发环境的AI助手**。许多用户使用Gemini定制他们的开发设置或修复机器上的问题：

* **编辑点文件：** 你可以通过引用它（`@~/.bashrc`）加载你的shell配置（`.bashrc` 或 `.zshrc`），然后要求Gemini CLI优化或排查它。例如，"我的 `PATH` 没有获取Go二进制文件，你能编辑我的 `.bashrc` 来修复吗？"AI可以插入正确的 `export` 行。在保存更改之前，它会显示差异供你确认。

* **诊断错误：** 如果你在终端或应用程序日志中遇到隐晦的错误，你可以复制它并将其提供给Gemini CLI。它将分析错误消息，通常会建议解决步骤。这类似于人们可能使用StackOverflow或Google的方式，但AI直接检查你的场景。例如："当我运行 `npm install` 时，我得到 `EACCES` 权限错误——我该如何修复？"Gemini可能检测到这是 `node_modules` 中的权限问题，并指导你更改目录所有权或使用适当的node版本管理器。

* **在项目外运行：** 默认情况下，如果你在没有 `.gemini` 上下文的目录中运行 `gemini`，这只意味着没有加载项目特定的上下文——但你仍然可以完全使用CLI。这对于临时任务（如系统故障排查）非常有用。你可能没有任何代码文件供它考虑，但你仍然可以通过它运行shell命令或让它获取网络信息。本质上，你将Gemini CLI视为一个可以为你*做事*的AI驱动终端，而不仅仅是聊天。

* **工作站定制：** 想要更改设置或安装新工具？你可以询问Gemini CLI，"在我的系统上安装Docker"或"配置我的Git以使用GPG签名提交。"CLI将尝试执行这些步骤。它可能会从网络获取说明（使用搜索工具），然后运行适当的shell命令。当然，始终观察它在做什么并批准命令——但它可以通过自动化多步设置过程来节省时间。一个真实例子：一个用户要求Gemini CLI"将我的macOS Dock首选项设置为自动隐藏并删除延迟"，AI能够执行必要的 `defaults write` 命令。

将这种模式视为使用Gemini CLI作为**智能shell**。实际上，你可以将此与技巧16（shell直通模式）结合使用——有时你可能会进入 `!` shell模式验证某些内容，然后返回AI模式让它分析输出。

**警告：** 进行系统级任务时，要小心具有广泛影响的命令（如 `rm -rf` 或系统配置更改）。Gemini CLI通常会要求确认，并且它不会在你看不到的情况下运行任何东西。但作为高级用户，你应该了解正在进行的更改。如果不确定，在运行之前要求Gemini解释命令（例如，"解释 `defaults write com.apple.dock autohide-delay -float 0` 做什么"——如果你以那种方式提示它，它会乐意解释而不是执行）。

**故障排查附加功能：** 另一个巧妙的用途是使用Gemini CLI解析日志或配置文件以查找问题。例如，"扫描这个Apache配置以查找错误"（使用 `@httpd.conf`），或"查看syslog中昨天下午2点左右的错误"（使用 `@/var/log/syslog`，如果可访问）。这就像有一个协同管理员。它甚至可以建议崩溃的可能原因或为常见错误模式提出修复方案。

总之，**不要犹豫启动Gemini CLI作为你的环境问题助手**。它在那里加速你所有的工作流程——不仅仅是编写代码，还包括维护你编写代码的系统。许多用户报告说，在Gemini的帮助下定制开发环境感觉就像有一个技术伙伴随时待命，处理繁琐或复杂的设置步骤。

## 技巧10：YOLO模式 - 自动批准工具操作（谨慎使用）

**快速用例：** 如果你感到自信（或冒险），你可以让Gemini CLI运行工具操作而无需每次都要求你的确认。这就是**YOLO模式**（You Only Live Once，你只活一次）。它通过 `--yolo` 标志或在[会话](https://www.philschmid.de/gemini-cli-cheatsheet#:~:text=,prompt%20in%20an%20external%20editor)期间按 `Ctrl+Y` 启用。在YOLO模式下，一旦AI决定使用工具（如运行shell命令或写入文件），它就会立即执行，而不会出现"批准？(y/n)"提示。

**为什么使用YOLO模式？** 主要是为了速度和便利，**当你信任AI的操作时**。有经验的用户可能会在执行大量重复的安全操作时打开YOLO。例如，如果你要求Gemini连续生成10个不同的文件，逐一批准可能会减慢流程；YOLO模式将让它们全部自动写入。另一个场景是在完全自动化的脚本或CI管道中使用Gemini CLI——你可能使用 `--yolo` 无头运行它，这样它就不会暂停以确认。

要从一开始就以YOLO模式启动，使用以下命令启动CLI：

```bash
gemini --yolo
```

或简写形式 `gemini -y`。你会在CLI中看到一些指示（如不同的提示符或通知），表明自动批准已[打开](https://www.philschmid.de/gemini-cli-cheatsheet#:~:text=initial%20prompt.%20%2A%20%60,to%20revert%20changes)。在交互会话期间，你可以随时按**Ctrl+Y**切换[它](https://www.philschmid.de/gemini-cli-cheatsheet#:~:text=,prompt%20in%20an%20external%20editor)——CLI通常会在页脚显示类似"YOLO模式已启用（所有操作自动批准）"的消息。

**重要警告：** YOLO模式功能强大但**有风险**。Gemini团队本身将其标记为"大胆用户"——意味着你应该意识到AI可能会在不询问的情况下执行危险命令。在正常模式下，如果AI决定运行 `rm -rf /`（最坏情况），你显然会拒绝。在YOLO模式下，该命令将立即运行（并可能毁掉你的一天）。虽然这种极端错误不太可能（AI的系统提示包括安全准则），但确认的全部意义在于捕捉任何不需要的操作。YOLO移除了那个安全网。

**YOLO的最佳实践：** 如果你想要一些便利而没有完全风险，考虑*白名单*特定命令。例如，你可以在设置中配置某些工具或命令模式不需要确认（如允许所有 `git` 命令或只读操作）。实际上，Gemini CLI支持在特定命令上跳过确认的配置：例如，你可以设置类似 `"tools.shell.autoApprove": ["git ", "npm test"]` 来始终运行[这些](https://google-gemini.github.io/gemini-cli/docs/cli/configuration.html#:~:text=match%20at%20L247%20%60%5B,Default%3A%20%60undefined)。这样，你可能不需要全局YOLO模式——你选择性地只对安全命令使用YOLO。另一种方法：在使用YOLO时在沙箱或容器中运行Gemini，这样即使它做了一些疯狂的事情，你的系统也是隔离的（Gemini有一个 `--sandbox` 标志在Docker [容器](https://www.philschmid.de/gemini-cli-cheatsheet#:~:text=echo%20,gemini)中运行工具）。

许多高级用户频繁地打开和关闭YOLO——在执行一系列小文件编辑或查询时打开它，在即将做一些关键事情时关闭它。你也可以这样做，使用键盘快捷键作为快速切换。

总之，**YOLO模式以监督为代价消除摩擦**。这是一个应谨慎和明智使用的专业功能。它真正展示了对AI的信任（或鲁莽！）。如果你是Gemini CLI的新手，在清楚了解它倾向于做什么的模式之前，你可能应该避免YOLO。如果你使用它，加倍使用版本控制或备份——以防万一。

*（如果有任何安慰的话，你并不孤单——社区中许多人开玩笑说"我YOLO了，Gemini做了一些疯狂的事情。"所以使用它，但是...嗯，你只活一次。）*

## 技巧11：无头模式和脚本模式（在后台运行Gemini CLI）

**快速用例：** 你可以通过以**无头模式**运行Gemini CLI在脚本或自动化中使用它。这意味着你通过命令行参数或环境变量提供提示（甚至完整对话），Gemini CLI生成输出并退出。这对于与其他工具集成或按计划触发AI任务非常有用。

例如，要在不打开REPL的情况下获得一次性答案，你已经看到可以使用 `gemini -p "...提示..."`。这已经是无头使用：它打印模型的响应并返回到[shell](https://www.philschmid.de/gemini-cli-cheatsheet#:~:text=Non,and%20get%20a%20single%20response)。但你可以做更多事情：

* **系统提示覆盖：** 如果你想使用自定义系统角色或指令集（与默认不同）运行Gemini CLI，可以使用环境变量 `GEMINI_SYSTEM_MD`。通过设置此变量，你告诉Gemini CLI忽略其内置系统提示，而使用你提供的文件[代替](https://medium.com/google-cloud/practical-gemini-cli-bring-your-own-system-instruction-19ea7f07faa2#:~:text=The%20,rather%20than%20its%20hardcoded%20defaults)。例如：

```bash
export GEMINI_SYSTEM_MD="/path/to/custom_system.md"
gemini -p "以高度谨慎执行任务X"
```

这将在执行[提示](https://medium.com/google-cloud/practical-gemini-cli-bring-your-own-system-instruction-19ea7f07faa2#:~:text=The%20feature%20is%20enabled%20by,specific%20configurations)之前加载你的 `custom_system.md` 作为系统提示（AI遵循的"角色"和规则）。或者，如果你设置 `GEMINI_SYSTEM_MD=true`，CLI将在当前项目的 `.gemini` [目录](https://medium.com/google-cloud/practical-gemini-cli-bring-your-own-system-instruction-19ea7f07faa2#:~:text=The%20feature%20is%20enabled%20by,specific%20configurations)中查找名为 `system.md` 的文件。这个功能非常高级——它本质上允许你用自己的指令*替换CLI的内置大脑*，一些用户为专门工作流程这样做（如模拟特定角色或强制执行超严格的策略）。小心使用，因为替换核心提示可能会影响工具使用（核心提示包含关于AI如何选择和使用[工具](https://medium.com/google-cloud/practical-gemini-cli-bring-your-own-system-instruction-19ea7f07faa2#:~:text=If%20you%20read%20my%20previous,proper%20functioning%20of%20Gemini%20CLI)的重要指示）。

* **通过CLI直接提示：** 除了 `-p`，还有 `-i`（交互式提示），它用初始提示启动会话，然后保持打开。例如：`gemini -i "你好，让我们调试一些东西"` 将打开REPL并已经向模型打过招呼了。如果你希望在启动时立即询问第一个问题，这很有用。

* **使用shell管道编写脚本：** 你不仅可以通过管道传递文本，还可以传递文件或命令输出到Gemini。例如：`gemini -p "总结这个日志：" < big_log.txt` 将 `big_log.txt` 的内容提供到提示中（在短语"总结这个日志："之后）。或者你可能做 `some_command | gemini -p "给定上述输出，出了什么问题？"`。这种技术允许你将Unix工具与AI分析组合。从某种意义上说，它是无头的，因为它是单次操作。

* **在CI/CD中运行：** 你可以将Gemini CLI集成到构建过程中。例如，CI管道可能运行测试，然后使用Gemini CLI自动分析失败的测试输出并发布评论。使用 `-p` 标志和环境验证，这可以脚本化。（当然，确保环境具有所需的API密钥或验证。）

另一个无头技巧：**`--format=json` 标志**（或配置设置）。如果你[配置](https://google-gemini.github.io/gemini-cli/docs/cli/configuration.html#:~:text=)，Gemini CLI可以以JSON格式输出响应而不是人类可读的文本。这对于程序化消费很有用——你的脚本可以解析JSON以获取答案或任何工具操作详细信息。

**为什么无头模式重要：** 它将Gemini CLI从交互式助手转变为其他程序可以调用的**后端服务**或实用程序。你可以安排每晚运行Gemini CLI提示的cron作业（想象生成报告或使用AI逻辑清理某些内容）。你可以在IDE中连接一个按钮，触发特定任务的无头Gemini运行。

**示例：** 假设你想要每日新闻网站的摘要。你可以有一个脚本：

```bash
gemini -p "网络获取\"https://news.site/top-stories\"并提取标题，然后将它们写入headlines.txt"
```

可能使用 `--yolo`，这样它就不会要求确认写入文件。这将使用网络获取工具获取页面和文件写入工具保存标题。全部自动，无需人工干预。一旦你将Gemini CLI视为可脚本化的组件，可能性就是无穷的。

总之，**无头模式**启用自动化。它是Gemini CLI与其他系统之间的桥梁。掌握它意味着你可以扩展AI使用——不仅仅是在终端中输入时，甚至当你不在时，你的AI智能体也可以为你工作。

*（提示：对于真正长时间运行的非交互式任务，你可能还会研究Gemini CLI的"计划"模式或它如何在无需干预的情况下生成多步骤计划。但是，这些是超出本范围的高级主题。在大多数情况下，通过无头模式精心设计的单个提示可以实现很多。）*

## 技巧12：保存和恢复聊天会话

**Quick use-case:** If you've been debugging an issue with Gemini CLI for an hour and need to stop, you don't have to lose the conversation context. Use `/chat save <name>` to save the session. Later (even after restarting the CLI), you can use `/chat resume <name>` to pick up where you left [off](https://www.philschmid.de/gemini-cli-cheatsheet#:~:text=,help%20information%20and%20available%20commands). This way, long-running conversations can be paused and continued seamlessly.

Gemini CLI essentially has a built-in chat session manager. The commands to know are:

* `/chat save <tag>` - Saves the current conversation state under a tag/name you [provide](https://www.philschmid.de/gemini-cli-cheatsheet#:~:text=,help%20information%20and%20available%20commands). The tag is like a filename or key for that session. Save often if you want, it will overwrite the tag if it exists. (Using a descriptive name is helpful - e.g., `chat save fix-docker-issue`.)

* `/chat list` - Lists all your saved sessions (the tags you've [used](https://www.philschmid.de/gemini-cli-cheatsheet#:~:text=,help%20information%20and%20available%20commands). This helps you remember what you named previous saves.

* `/chat resume <tag>` - Resumes the session with that tag, restoring the entire conversation context and history to how it was when [saved](https://www.philschmid.de/gemini-cli-cheatsheet#:~:text=,help%20information%20and%20available%20commands). It's like you never left. You can then continue chatting from that point.

* `/chat share` - (saves to file) This is useful as you can share the entire chat with someone else who can continue the session. Almost collaboration-like.

Under the hood, these sessions are stored likely in `~/.gemini/chats/` or a similar location. They include the conversation messages and any relevant state. This feature is super useful for cases such as:

* **Long debugging sessions:** Sometimes debugging with an AI can be a long back-and-forth. If you can't solve it in one go, save it and come back later (maybe with a fresh mind). The AI will still "remember" everything from before, because the whole context is reloaded.

* **Multi-day tasks:** If you're using Gemini CLI as an assistant for a project, you might have one chat session for "Refactor module X" that spans multiple days. You can resume that specific chat each day so the context doesn't reset daily. Meanwhile, you might have another session for "Write documentation" saved separately. Switching contexts is just a matter of saving one and resuming the other.

* **Team hand-off:** This is more experimental, but in theory, you could share the content of a saved chat with a colleague (the saved files are likely portable). If they put it in their `.gemini` directory and resume, they could see the same context. The **practical simpler approach** for collaboration is just copying the relevant Q&A from the log and using a shared `GEMINI.md` or prompt, but it's interesting to note that the session data is yours to keep.

**Usage example:**

```bash
/chat save api-upgrade
```

*(Session saved as "api-upgrade")*

```bash
/quit
```

*(Later, reopen CLI)*

```bash
$ gemini
gemini> /chat list
```

*(Shows: api-upgrade)*

```bash
gemini> /chat resume api-upgrade
```

Now the model greets you with the last exchange's state ready. You can confirm by scrolling up that all your previous messages are present.

**Pro Tip:** Use meaningful tags when saving [chats](https://medium.com/@ferreradaniel/gemini-cli-free-ai-tool-upgrade-5-new-features-you-need-right-now-04cfefac5e93#:~:text=Naming%20conventions%20to%20keep%20projects,organized). Instead of `/chat save session1`, give it a name related to the topic (e.g. `/chat save memory-leak-bug`). This will help you find the right one later via `/chat list`. There is no strict limit announced on how many sessions you can save, but cleaning up old ones occasionally might be wise just for organization.

This feature turns Gemini CLI into a persistent advisor. You don't lose knowledge gained in a conversation; you can always pause and resume. It's a differentiator compared to some other AI interfaces that forget context when closed. For power users, it means **you can maintain parallel threads of work** with the AI. Just like you'd have multiple terminal tabs for different tasks, you can have multiple chat sessions saved and resume the one you need at any given time.

## Tip 13: Multi-Directory Workspace - One Gemini, Many Folders

**Quick use-case:** Do you have a project split across multiple repositories or directories? You can launch Gemini CLI with access to *all of them* at once, so it sees a unified workspace. For example, if your frontend and backend are separate folders, you can include both so that Gemini can edit or reference files in both.

There are two ways to use **multi-directory mode**:

* **Launch flag:** Use the `--include-directories` (or `-I`) flag when starting Gemini CLI. For example:

```bash
gemini --include-directories "../backend:../frontend"
```

This assumes you run the command from, say, a `scripts` directory and want to include two sibling folders. You provide a colon-separated list of paths. Gemini CLI will then treat all those directories as part of one big workspace.

* **Persistent setting:** In your `settings.json`, you can define `"includeDirectories": ["path1", "path2", [...]](https://www.philschmid.de/gemini-cli-cheatsheet#:~:text=,61AFEF%22%2C%20%22AccentPurple)`. This is useful if you always want certain common directories loaded (e.g., a shared library folder that multiple projects use). The paths can be relative or absolute. Environment variables in the paths (like `~/common-utils`) are [allowed](https://www.philschmid.de/gemini-cli-cheatsheet#:~:text=,61AFEF%22%2C%20%22AccentPurple).

When multi-dir mode is active, the CLI's context and tools consider files across all included locations. The `> /directory show` command will list which directories are in the current [workspace](https://medium.com/@ferreradaniel/gemini-cli-free-ai-tool-upgrade-5-new-features-you-need-right-now-04cfefac5e93#:~:text=How%20to%20add%20multiple%20directories,step). You can also dynamically add directories during a session with `/directory add [<path>](https://medium.com/@ferreradaniel/gemini-cli-free-ai-tool-upgrade-5-new-features-you-need-right-now-04cfefac5e93#:~:text=How%20to%20add%20multiple%20directories,step)` - it will then load that on the fly (potentially scanning it for context like it does on startup).

**Why use multi-directory mode?** In microservice architectures or modular codebases, it's common that one piece of code lives in one repo and another piece in a different repo. If you only ran Gemini in one, it wouldn't "see" the others. By combining them, you enable cross-project reasoning. For example, you could ask, "Update the API client in the frontend to match the backend's new API endpoints" - Gemini can open the backend folder to see the API definitions and simultaneously open the frontend code to modify it accordingly. Without multi-dir, you'd have to do one side at a time and manually carry info over.

**Example:** Let's say you have `client/` and `server/`. You start:

```bash
cd client
gemini --include-directories "../server"
```

Now at the `gemini>` prompt, if you do `> !ls`, you'll see it can list files in both `client` and `server` (it might show them as separate paths). You could do:

```bash
Open server/routes/api.py and client/src/api.js side by side to compare function names.
```

The AI will have access to both files. Or you might say:

```bash
The API changed: the endpoint "/users/create" is now "/users/register". Update both backend and frontend accordingly.
```

It can simultaneously create a patch in the backend route and adjust the frontend fetch call.

Under the hood, Gemini merges the file index of those directories. There might be some performance considerations if each directory is huge, but generally it handles multiple small-medium projects fine. The cheat sheet notes that this effectively creates one workspace with multiple [roots](https://www.philschmid.de/gemini-cli-cheatsheet#:~:text=%22includeDirectories%22%3A%20%5B%22..%2Fshared,98C379%22%2C%20%22AccentYellow).

**Tip within a tip:** Even if you don't use multi-dir all the time, know that you can still reference files across the filesystem by absolute path in prompts (`@/path/to/file`). However, without multi-dir, Gemini might not have permission to edit those or know to load context from them proactively. Multi-dir formally includes them in scope so it's aware of all files for tasks like search or code generation across the whole set.

**Remove directories:** If needed, `/directory remove <path>` (or a similar command) can drop a directory from the workspace. This is less common, but maybe if you included something accidentally, you can remove it.

In summary, **multi-directory mode unifies your context**. It's a must-have for polyrepo projects or any situation where code is split up. It makes Gemini CLI act more like an IDE that has your entire solution open. As a pro user, this means no part of your project is out of the AI's reach.

## Tip 14: Organize and Clean Up Your Files with AI Assistance

**Quick use-case:** Tired of a messy `Downloads` folder or disorganized project assets? You can enlist Gemini CLI to act as a smart organizer. By providing it an overview of a directory, it can classify files and even move them into subfolders (with your approval). For instance, "Clean up my `Downloads`: move images to an `Images` folder, PDFs to `Documents`, and delete temporary files."

Because Gemini CLI can read file names, sizes, and even peek into file contents, it can make informed decisions about file [organization](https://github.com/google-gemini/gemini-cli/discussions/7890#:~:text=We%20built%20a%20CLI%20tool,trash%20folder%20for%20manual%20deletion). One community-created tool dubbed **"Janitor AI"** showcases this: it runs via Gemini CLI to categorize files as important vs junk, and groups them [accordingly](https://github.com/google-gemini/gemini-cli/discussions/7890#:~:text=We%20built%20a%20CLI%20tool,trash%20folder%20for%20manual%20deletion). The process involved scanning the directory, using Gemini's reasoning on filenames and metadata (and content if needed), then moving files into categories. Notably, it didn't automatically delete junk - rather, it moved them to a `Trash` folder for [review](https://github.com/google-gemini/gemini-cli/discussions/7890#:~:text=organize%20files,trash%20folder%20for%20manual%20deletion).

Here's how you might replicate such a workflow with Gemini CLI manually:

1. **Survey the directory:** Use a prompt to have Gemini list and categorize. For example:

```bash
List all files in the current directory and categorize them as "images", "videos", "documents", "archives", or "others".
```

Gemini might use `!ls` or similar to get the file list, then analyze the names/extensions to produce categories.

1. **Plan the organization:** Ask Gemini how it would like to reorganize. For example:

```bash
Propose a new folder structure for these files. I want to separate by type (Images, Videos, Documents, etc.). Also identify any files that seem like duplicates or unnecessary.
```

The AI might respond with a plan: e.g., *"Create folders: `Images/`, `Videos/`, `Documents/`, `Archives/`. Move `X.png`, `Y.jpg` to `Images/`; move `A.mp4` to `Videos/`; etc. The file `temp.txt` looks unnecessary (maybe a temp file)."*

1. **Execute moves with confirmation:** You can then instruct it to carry out the plan. It may use shell commands like `mv` for each file. Since this modifies your filesystem, you'll get confirmation prompts for each (unless you YOLO it). Carefully approve the moves. After completion, your directory will be neatly organized as suggested.

Throughout, Gemini's natural language understanding is key. It can reason, for instance, that `IMG_001.png` is an image or that `presentation.pdf` is a document, even if not explicitly stated. It can even open an image (using its vision capability) to see what's in it - e.g., differentiating between a screenshot vs a photo vs an icon - and name or sort it [accordingly](https://dev.to/therealmrmumba/7-insane-gemini-cli-tips-that-will-make-you-a-superhuman-developer-2d7h#:~:text=If%20your%20project%20folder%20is,using%20relevant%20and%20descriptive%20terms).

**Renaming files by content:** A particularly magical use is having Gemini rename files to be more descriptive. The Dev Community article "7 Insane Gemini CLI Tips" describes how Gemini can **scan images and automatically rename them** based on their [content](https://dev.to/therealmrmumba/7-insane-gemini-cli-tips-that-will-make-you-a-superhuman-developer-2d7h#:~:text=If%20your%20project%20folder%20is,using%20relevant%20and%20descriptive%20terms). For example, a file named `IMG_1234.jpg` might be renamed to `login_screen.jpg` if the AI sees it's a screenshot of a login [screen](https://dev.to/therealmrmumba/7-insane-gemini-cli-tips-that-will-make-you-a-superhuman-developer-2d7h#:~:text=If%20your%20project%20folder%20is,using%20relevant%20and%20descriptive%20terms). To do this, you could prompt:

```bash
For each .png image here, look at its content and rename it to something descriptive.
```

Gemini will open each image (via vision tool), get a description, then propose a `mv IMG_1234.png login_screen.png` [action](https://dev.to/therealmrmumba/7-insane-gemini-cli-tips-that-will-make-you-a-superhuman-developer-2d7h#:~:text=If%20your%20project%20folder%20is,using%20relevant%20and%20descriptive%20terms). This can dramatically improve the organization of assets, especially in design or photo folders.

**Two-pass approach:** The Janitor AI discussion noted a two-step process: first broad categorization (important vs junk vs other), then refining [groups](https://github.com/google-gemini/gemini-cli/discussions/7890#:~:text=organize%20files,trash%20folder%20for%20manual%20deletion). You can emulate this: first separate files that likely can be deleted (maybe large installer `.dmg` files or duplicates) from those to keep. Then focus on organizing the keepers. Always double-check what the AI flags as junk; its guess might not always be right, so manual oversight is needed.

**Safety tip:** When letting the AI loose on file moves or deletions, have backups or at least be ready to undo (with `/restore` or your own backup). It's wise to do a dry-run: ask Gemini to print the commands it *would* run to organize, without executing them, so you can review. For instance: "List the `mv` and `mkdir` commands needed for this plan, but don't execute them yet." Once you review the list, you can either copy-paste execute them, or instruct Gemini to proceed.

This is a prime example of using Gemini CLI for "non-obvious" tasks - it's not just writing code, it's doing **system housekeeping with AI smarts**. It can save time and bring a bit of order to chaos. After all, as developers we accumulate clutter (logs, old scripts, downloads), and an AI janitor can be quite handy.

## Tip 15: Compress Long Conversations to Stay Within Context

**Quick use-case:** If you've been chatting with Gemini CLI for a long time, you might hit the model's context length limit or just find the session getting unwieldy. Use the `/compress` command to summarize the conversation so far, replacing the full history with a concise [summary](https://www.philschmid.de/gemini-cli-cheatsheet#:~:text=Command%20Description%20,files). This frees up space for more discussion without starting from scratch.

Large language models have a fixed context window (Gemini 2.5 Pro's is very large, but not infinite). If you exceed it, the model may start forgetting earlier messages or lose coherence. The `/compress` feature is essentially an **AI-generated tl;dr** of your session that keeps important points.

**How it works:** When you type `/compress`, Gemini CLI will take the entire conversation (except system context) and produce a summary. It then replaces the chat history with that summary as a single system or assistant message, preserving essential details but dropping minute-by-minute dialogue. It will indicate that compression happened. For example, after `/compress`, you might see something like:

\--- Conversation compressed \---  
Summary of discussion: The user and assistant have been debugging a memory leak in an application. Key points: The issue is likely in `DataProcessor.js`, where objects aren't being freed. The assistant suggested adding logging and identified a possible infinite loop. The user is about to test a fix.  
\--- End of summary \---

From that point on, the model only has that summary (plus new messages) as context for what happened before. This usually is enough if the summary captured the salient info.

**When to compress:** Ideally before you *hit* the limit. If you notice the session is getting lengthy (several hundred turns or a lot of code in context), compress proactively. The cheat sheet mentions an automatic compression setting (e.g., compress when context exceeds 60% of [max](https://www.philschmid.de/gemini-cli-cheatsheet#:~:text=%22includeDirectories%22%3A%20%5B%22..%2Fshared,98C379%22%2C%20%22AccentYellow)). If you enable that, Gemini might auto-compress and let you know. Otherwise, manual `/compress` is in your toolkit.

**After compressing:** You can continue the conversation normally. If needed, you can compress multiple times in a very long session. Each time, you lose some granularity, so don't compress too frequently for no reason - you might end up with an overly brief remembrance of a complex discussion. But generally the model's own summarization is pretty good at keeping the key facts (and you can always restate anything critical yourself).

**Context window example:** Let's illustrate. Suppose you fed in a large codebase by referencing many files and had a 1M token context (the max). If you then want to shift to a different part of the project, rather than starting a new session (losing all that understanding), you could compress. The summary will condense the knowledge gleaned from the code (like "We loaded modules A, B, C. A has these functions... B interacts with C in these ways..."). Now you can proceed to ask about new things with that knowledge retained abstractly.

**Memory vs Compression:** Note that compression doesn't save to long-term memory, it's local to the conversation. If you have facts you *never* want lost, consider Tip 4 (adding to `/memory`) - because memory entries will survive compression (they'll just be reinserted anyway since they are in `GEMINI.md` context). Compression is more about ephemeral chat content.

**A minor caution:** after compression, the AI's style might slightly change because it's effectively seeing a "fresh" conversation with a summary. It might reintroduce itself or change tone. You can instruct it like "Continue from here... (we compressed)" to smooth it out. In practice, it often continues fine.

To summarize (pun intended), **use `/compress` as your session grows long** to maintain performance and relevance. It helps Gemini CLI focus on the bigger picture instead of every detail of the conversation's history. This way, you can have marathon debugging sessions or extensive design discussions without running out of the "mental paper" the AI is writing on.

## Tip 16: Passthrough Shell Commands with `!` (Talk to Your Terminal)

**Quick use-case:** At any point in a Gemini CLI session, you can run actual shell commands by prefixing them with `!`. For example, if you want to check the git status, just type `!git status` and it will execute in your [terminal](https://www.philschmid.de/gemini-cli-cheatsheet#:~:text=Run%20a%20single%20command%3A). This saves you from switching windows or context - you're still in the Gemini CLI, but you're essentially telling it "let me run this command real quick."

This tip is about **Shell Mode** in Gemini CLI. There are two ways to use it:

* **Single command:** Just put `!` at the start of your prompt, followed by any command and arguments. This will execute that command in the current working directory and display the output [in-line](https://www.philschmid.de/gemini-cli-cheatsheet#:~:text=Run%20shell%20commands%20directly%20in,the%20CLI). For example:

```bash
!ls -lh src/
```

will list the files in the `src` directory, outputting something like you'd see in a normal terminal. After the output, the Gemini prompt returns so you can continue chatting or issue more commands.

* **Persistent shell mode:** If you enter `!` alone and hit Enter, Gemini CLI switches into a sub-mode where you get a shell prompt (often it looks like `shell>` or [similar](https://www.philschmid.de/gemini-cli-cheatsheet#:~:text=). Now you can type multiple shell commands interactively. It's basically a mini-shell within the CLI. You exit this mode by typing `!` on an empty line again (or `exit`). For instance:

```bash
!
shell> pwd
/home/alice/project
shell> python --version
Python 3.x.x
shell> !
```

After the final `!`, you're back to the normal Gemini prompt.

**Why is this useful?** Because development is a mix of actions and inquiries. You might be discussing something with the AI and realize you need to compile the code or run tests to see something. Instead of leaving the conversation, you can quickly do it and feed the result back into the chat. In fact, Gemini CLI often does this for you as part of its tool usage (it might automatically run `!pytest` when you ask to fix tests, for [example](https://genmind.ch/posts/Howto-Supercharge-Your-Terminal-with-Gemini-CLI/#:~:text=)). But as the user, you have full control to do it manually too.

**Examples:**

* After Gemini suggests a fix in code, you can do `!npm run build` to see if it compiles, then copy any errors and ask Gemini to help with those.

* If you want to open a file in `vim` or `nano`, you could even launch it via `!nano filename` (though note that since Gemini CLI has its own interface, using an interactive editor inside it might be a bit awkward - better to use the built-in editor integration or copy to your editor).

* You can use shell commands to gather info for the AI: e.g., `!grep TODO -R .` to find all TODOs in the project, then you might ask Gemini to help address those TODOs.

* Or simply use it for environment tasks: `!pip install some-package` if needed, etc., without leaving the CLI.

**Seamless interplay:** One cool aspect is how the conversation can refer to outputs. For example, you could do `!curl http://example.com` to fetch some data, see the output, then immediately say to Gemini, "Format the above output as JSON" - since the output was printed in the chat, the AI has it in context to work with (provided it's not too large).

**Terminal as a default shell:** If you find yourself always prefacing commands with `!`, you can actually make the shell mode persistent by default. One way is launching Gemini CLI with a specific tool mode (there's a concept of default tool). But easier: just drop into shell mode (`!` with nothing) at session start if you plan to run a lot of manual commands and only occasionally talk to AI. Then you can exit shell mode whenever you want to ask a question. It's almost like turning Gemini CLI into your normal terminal that happens to have an AI readily available.

**Integration with AI planning:** Sometimes Gemini CLI itself will propose to run a shell command. If you approve, it effectively does the same as `!command`. Understanding that, you know you can always intervene. If Gemini is stuck or you want to try something, you don't have to wait for it to suggest - you can just do it and then continue.

In summary, the `!` **passthrough** means *you don't have to leave Gemini CLI for shell tasks*. It collapses the boundary between chatting with the AI and executing commands on your system. As a pro user, this is fantastic for efficiency - your AI and your terminal become one continuous environment.

## Tip 17: Treat Every CLI Tool as a Potential Gemini Tool

**Quick use-case:** Realize that Gemini CLI can leverage **any** command-line tool installed on your system as part of its problem-solving. The AI has access to the shell, so if you have `cURL`, `ImageMagick`, `git`, `Docker`, or any other tool, Gemini can invoke it when appropriate. In other words, *your entire `$PATH` is the AI's toolkit*. This greatly expands what it can do - far beyond its built-in tools.

For example, say you ask: "Convert all PNG images in this folder to WebP format." If you have ImageMagick's `convert` utility installed, Gemini CLI might plan something like: use a shell loop with `convert` command for each [file](https://genmind.ch/posts/Howto-Supercharge-Your-Terminal-with-Gemini-CLI/#:~:text=%3E%20%21for%20f%20in%20,png%7D.webp%22%3B%20done). Indeed, one of the earlier examples from a blog showed exactly this, where the user prompted to batch-convert images, and Gemini executed a shell one-liner with the `convert` [tool](https://genmind.ch/posts/Howto-Supercharge-Your-Terminal-with-Gemini-CLI/#:~:text=).

Another scenario: "Deploy my app to Docker." If `Docker CLI` is present, the AI could call `docker build` and `docker run` steps as needed. Or "Use FFmpeg to extract audio from `video.mp4`" - it can construct the `ffmpeg` command.

This tip is about mindset: **Gemini isn't limited to what's coded into it** (which is already extensive). It can figure out how to use other programs available to achieve a [goal](https://medium.com/google-cloud/gemini-cli-tutorial-series-part-4-built-in-tools-c591befa59ba#:~:text=In%20this%20part%2C%20we%20looked,In%20the%20next%20part%2C%20we). It knows common syntax and can read help texts if needed (it could call `--help` on a tool). The only limitation is safety: by default, it will ask confirmation for any `run_shell_command` it comes up with. But as you become comfortable, you might allow certain benign commands automatically (see YOLO or allowed-tools config).

**Be mindful of the environment:** "With great power comes great responsibility." Since every shell tool is fair game, you should ensure that your `$PATH` doesn't include anything you wouldn't want the AI to run inadvertently. This is where Tip 19 (custom PATH) comes in - some users create a restricted `$PATH` for Gemini, so it can't, say, directly call system destructive commands or maybe not call `gemini` recursively (to avoid loops). The point is, by default if `gcc` or `terraform` or anything is in `$PATH`, Gemini could invoke it. It doesn't mean it will randomly do so - only if the task calls for it - but it's possible.

**Train of thought example:** Imagine you ask Gemini CLI: "Set up a basic HTTP server that serves the current directory." The AI might think: "I can use Python's built-in server for this." It then issues `!python3 -m http.server 8000`. Now it just used a system tool (Python) to launch a server. That's an innocuous example. Another: "Check the memory usage on this Linux system." The AI might use the `free -h` command or read from `/proc/meminfo`. It's effectively doing what a sysadmin would do, by using available commands.

**All tools are extensions of the AI:** This is somewhat futuristic, but consider that any command-line program can be seen as a "function" the AI can call to extend its capability. Need to solve a math problem? It could call `bc` (calculator). Need to manipulate an image? It could call an image processing tool. Need to query a database? If the CLI client is installed and credentials are there, it can use it. The possibilities are expansive. In other AI agent frameworks, this is known as tool use, and Gemini CLI is designed with a lot of trust in its agent to decide the right [tool](https://cloud.google.com/blog/topics/developers-practitioners/agent-factory-recap-deep-dive-into-gemini-cli-with-taylor-mullen#:~:text=The%20Gemini%20CLI%20%20is,understanding%20of%20the%20developer%20workflow).

**When it goes wrong:** The flip side is if the AI misunderstands a tool or has a hallucination about one. It might try to call a command that doesn't exist, or use wrong flags, resulting in errors. This isn't a big deal - you'll see the error and can correct or clarify. In fact, the system prompt of Gemini CLI likely guides it to first do a dry-run (just propose the command) rather than executing blindly. So you often get a chance to catch these. Over time, the developers are improving the tool selection logic to reduce these missteps.

The main takeaway is to **think of Gemini CLI as having a very large Swiss Army knife** - not just the built-in blades, but every tool in your OS. You don't have to instruct it on how to use them if it's something standard; usually it knows or can find out. This significantly amplifies what you can accomplish. It's like having a junior dev or devops engineer who knows how to run pretty much any program you have installed.

As a pro user, you can even install additional CLI tools specifically to give Gemini more powers. For example, if you install a CLI for a cloud service (AWS CLI, GCloud CLI, etc.), in theory Gemini can utilize it to manage cloud resources if prompted to. Always ensure you understand and trust the commands run, especially with powerful tools (you wouldn't want it spinning up huge cloud instances accidentally). But used wisely, this concept - **everything is a Gemini tool** - is what makes it *exponentially* more capable as you integrate it into your environment.

## Tip 18: Utilize Multimodal AI - Let Gemini See Images and More

**Quick use-case:** Gemini CLI isn't limited to text - it's multimodal. This means it can analyze images, diagrams, or even PDFs if given. Use this to your advantage. For instance, you could say "Here's a screenshot of an error dialog, `@./error.png` - help me troubleshoot this." The AI will "see" the image and respond accordingly.

One of the standout features of Google's Gemini model (and its precursor PaLM2 in Codey form) is image understanding. In Gemini CLI, if you reference an image with `@`, the model receives the image data. It can output descriptions, classifications, or reason about the image's content. We already discussed renaming images by content (Tip 14) and describing screenshots (Tip 7). But let's consider other creative uses:

* **UI/UX feedback:** If you're a developer working with designers, you can drop a UI image and ask Gemini for feedback or to generate code. "Look at this UI mockup `@mockup.png` and produce a React component structure for it." It could identify elements in the image (header, buttons, etc.) and outline code.

* **Organizing images:** Beyond renaming, you might have a folder of mixed images and want to sort by content. "Sort the images in `./photos/` into subfolders by theme (e.g., sunsets, mountains, people)." The AI can look at each photo and categorize it (this is similar to what some photo apps do with AI - now you can do it with your own script via Gemini).

* **OCR and data extraction:** If you have a screenshot of error text or a photo of a document, Gemini can often read the text from it. For example, "Extract the text from `invoice.png` and put it into a structured format." As shown in a Google Cloud blog example, Gemini CLI can process a set of invoice images and output a table of their [info](https://medium.com/google-cloud/gemini-cli-tutorial-series-part-4-built-in-tools-c591befa59ba#:~:text=Press%20enter%20or%20click%20to,view%20image%20in%20full%20size). It basically did OCR + understanding to get invoice numbers, dates, amounts from pictures of invoices. That's an advanced use-case but entirely possible with the multimodal model under the hood.

* **Understanding graphs or charts:** If you have a graph screenshot, you could ask "Explain this chart's key insights `@chart.png`." It might interpret the axes and trends. Accuracy can vary, but it's a nifty try.

To make this practical: when you `@image.png`, ensure the image isn't too huge (though the model can handle reasonably large images). The CLI will likely encode it and send it to the model. The response might include descriptions or further actions. You can mix text and image references in one prompt too.

**Non-image modalities:** The CLI and model potentially can handle PDFs and audio too, by converting them via tools. For example, if you `@report.pdf`, Gemini CLI might use a PDF-to-text tool under the hood to extract text and then summarize. If you `@audio.mp3` and ask for a transcript, it might use an audio-to-text tool (like a speech recognition function). The cheat sheet suggests referencing PDFs, audio, video files is [supported](https://www.philschmid.de/gemini-cli-cheatsheet#:~:text=Reference%20files%20or%20directories%20in,PDFs%2C%20audio%2C%20and%20video%20files), presumably by invoking appropriate internal tools or APIs. So, "transcribe this interview audio: `@interview.wav`" could actually work (if not now, likely soon, since underlying Google APIs for speech-to-text could be plugged in).

**Rich outputs:** Multimodal also means the AI can return images in responses if integrated (though in CLI it usually won't *display* them directly, but it could save an image file or output ASCII art, etc.). The MCP capability mentioned that tools can return [images](https://www.philschmid.de/gemini-cli-cheatsheet#:~:text=Capabilities%3A). For instance, an AI drawing tool could generate an image and Gemini CLI could present it (maybe by opening it or giving a link).

**Important:** The CLI itself is text-based, so you won't *see* the image in the terminal (unless it's capable of ASCII previews). You'll just get the analysis. So this is mostly about reading images, not displaying them. If you're in VS Code integration, it might show images in the chat view.

In summary, **don't forget the "I" in GUI when using Gemini CLI** - it can handle the visual just as well as the textual in many cases. This opens up workflows like visual debugging, design help, data extraction from screenshots, etc., all under the same tool. It's a differentiator that some other CLI tools may not have yet. And as models improve, this multimodal support will only get more powerful, so it's a future-proof skill to exploit.

## Tip 19: Customize the `$PATH` (and Tool Availability) for Stability

**Quick use-case:** If you ever find Gemini CLI getting confused or invoking the wrong programs, consider running it with a tailored `$PATH`. By limiting or ordering the available executables, you can prevent the AI from, say, calling a similarly named script that you didn't intend. Essentially, you sandbox its tool access to known-good tools.

For most users, this isn't an issue, but for pro users with lots of custom scripts or multiple versions of tools, it can be helpful. One reason mentioned by the developers is avoiding infinite loops or weird [behavior](https://github.com/google-gemini/gemini-cli/discussions/7890#:~:text=We%20built%20a%20CLI%20tool,trash%20folder%20for%20manual%20deletion). For example, if `gemini` itself is in `$PATH`, an AI gone awry might recursively call `gemini` from within Gemini (a strange scenario, but theoretically possible). Or perhaps you have a command named `test` that conflicts with something - the AI might call the wrong one.

**How to set PATH for Gemini:** Easiest is inline on launch:

```bash
PATH=/usr/bin:/usr/local/bin gemini
```

This runs Gemini CLI with a restricted `$PATH` of just those directories. You might exclude directories where experimental or dangerous scripts lie. Alternatively, create a small shell script wrapper that purges or adjusts `$PATH` then exec's `gemini`.

Another approach is using environment or config to explicitly disable certain tools. For instance, if you absolutely never want the AI to use `rm` or some destructive tool, you could technically create an alias or dummy `rm` in a safe `$PATH` that does nothing (though this could interfere with normal operations, so maybe not that one). A better method is the **exclude list** in settings. In an extension or `settings.json`, you can exclude tool [names](https://www.philschmid.de/gemini-cli-cheatsheet#:~:text=). E.g.,

```json
"excludeTools": ["run_shell_command"]
```

This extreme example would stop *all* shell commands from running (making Gemini effectively read-only). More granular, there was mention of skipping confirmation for some; similarly you might configure something like:

```json
"tools": {
  "exclude": ["apt-get", "shutdown"]
}
```

*(This syntax is illustrative; consult docs for exact usage.)*

The principle is, by controlling the environment, you reduce risk of the AI doing something dumb with a tool it shouldn't. It's akin to child-proofing the house.

**Prevent infinite loops:** One user scenario was a loop where Gemini kept reading its own output or re-reading files [repeatedly](https://support.google.com/gemini/thread/337650803/infinite-loops-with-tool-code-in-answers?hl=en#:~:text=Community%20support,screen%20with%20weird%20scrolling). Custom `$PATH` can't directly fix logic loops, but one cause could be if the AI calls a command that triggers itself. Ensuring it can't accidentally spawn another AI instance (like calling `bard` or `gemini` command, if it thought to do so) is good. Removing those from `$PATH` (or renaming them for that session) helps.

**Isolation via sandbox:** Another alternative to messing with `$PATH` is using `--sandbox` mode (which uses Docker or Podman to run tools in an isolated [environment](https://www.philschmid.de/gemini-cli-cheatsheet#:~:text=echo%20,gemini)). In that case, the AI's actions are contained and have only the tools that sandbox image provides. You could supply a Docker image with a curated set of tools. This is heavy-handed but very safe.

**Custom PATH for specific tasks:** You might have different `$PATH` setups for different projects. For example, in one project you want it to use a specific version of Node or a local toolchain. Launching `gemini` with the `$PATH` that points to those versions will ensure the AI uses the right one. Essentially, treat Gemini CLI like any user - it uses whatever environment you give it. So if you need it to pick `gcc-10` vs `gcc-12`, adjust `$PATH` or `CC` env var accordingly.

**In summary:** *Guard rails.* As a power user, you have the ability to fine-tune the operating conditions of the AI. If you ever find a pattern of undesirable behavior tied to tool usage, tweaking `$PATH` is a quick remedy. For everyday use, you likely won't need this, but it's a pro tip to keep in mind if you integrate Gemini CLI into automation or CI: give it a controlled environment. That way, you know exactly what it can and cannot do, which increases reliability.

---

## Tip 20: Track and reduce token spend with token caching and stats

If you run long chats or repeatedly attach the same big files, you can cut cost and latency by turning on token caching and monitoring usage. With an API key or Vertex AI auth, Gemini CLI automatically reuses previously sent system instructions and context, so follow‑up requests are cheaper. You can see the savings live in the CLI.

**How to use it**

Use an auth mode that enables caching. Token caching is available when you authenticate with a Gemini API key or Vertex AI. It is not available with OAuth login today. [Google Gemini](https://google-gemini.github.io/gemini-cli/docs/cli/token-caching.html)

Inspect your usage and cache hits. Run the `stats` command during a session. It shows total tokens and a `cached` field when caching is active.

```bash
/stats
```

The command's description and cached reporting behavior are documented in the commands reference and FAQ. [Google Gemini+1](https://google-gemini.github.io/gemini-cli/docs/cli/commands.html?utm_source=chatgpt.com)

Capture metrics in scripts. When running headless, output JSON and parse the `stats` block, which includes `tokens.cached` for each model:

```bash
gemini -p "Summarize README" --output-format json
```

The headless guide documents the JSON schema with cached token counts. [Google Gemini](https://google-gemini.github.io/gemini-cli/docs/cli/headless.html)

Save a session summary to file: For CI or budget tracking, write a JSON session summary to disk.

```bash
gemini -p "Analyze logs" --session-summary usage.json
```

This flag is listed in the changelog. [Google Gemini](https://google-gemini.github.io/gemini-cli/docs/changelogs/)

With API key or Vertex auth, the CLI automatically reuses previously sent context so later turns send fewer tokens. Keeping `GEMINI.md` and large file references stable across turns increases cache hits; you'll see that reflected in stats as cached tokens.

## Tip 21: Use `/copy` for Quick Clipboard Copy

**Quick use-case:** Instantly copy the latest answer or code snippet from Gemini CLI to your system clipboard, without any extraneous formatting or line [numbers](https://google-gemini.github.io/gemini-cli/docs/cli/commands.html#:~:text=,for%20easy%20sharing%20or%20reuse). This is perfect for quickly pasting AI-generated code into your editor or sharing a result with a teammate.

When Gemini CLI provides an answer (especially a multi-line code block), you often want to reuse it elsewhere. The `/copy` slash command makes this effortless by copying *the last output produced by the CLI* directly to your [clipboard](https://google-gemini.github.io/gemini-cli/docs/cli/commands.html#:~:text=,for%20easy%20sharing%20or%20reuse). Unlike manual selection (which can grab line numbers or prompt text), `/copy` grabs only the raw response content. For example, if Gemini just generated a 50-line Python script, simply typing `/copy` will put that entire script into your clipboard, ready to paste - no need to scroll and select text. Under the hood, Gemini CLI uses the appropriate clipboard utility for your platform (e.g. `pbcopy` on macOS, `clip` on [Windows](https://google-gemini.github.io/gemini-cli/docs/cli/commands.html#:~:text=,clip). Once you run the command, you'll typically see a confirmation message, and then you can paste the copied text wherever you need it.

**How it works:** The `/copy` command requires that your system has a clipboard tool [available](https://google-gemini.github.io/gemini-cli/docs/cli/commands.html#:~:text=,clip). On macOS and Windows, the required tools (`pbcopy` and `clip` respectively) are usually pre-installed. On Linux, you may need to install `xclip` or `xsel` for `/copy` to [function](https://google-gemini.github.io/gemini-cli/docs/cli/commands.html#:~:text=,clip). After ensuring that, you can use `/copy` anytime after Gemini CLI prints an answer. It will capture the *entire* last response (even if it's long) and omit any internal numbering or formatting the CLI may show on-screen. This saves you from dealing with unwanted artifacts when transferring the content. It's a small feature, but a huge time-saver when you're iterating on code or compiling a report generated by the AI.

**Pro Tip:** If you find the `/copy` command isn't working, double-check that your clipboard utilities are installed and accessible. For instance, Ubuntu users should run `sudo apt install xclip` to enable clipboard [copying](https://google-gemini.github.io/gemini-cli/docs/cli/commands.html#:~:text=,clip). Once set up, `/copy` lets you share Gemini's outputs with zero friction - copy, paste, and you're done.

## Tip 22: Master `Ctrl+C` for Shell Mode and Exiting

**Quick use-case:** Cleanly interrupt Gemini CLI or exit shell mode with a single keypress - and quit the CLI entirely with a quick double-tap - thanks to the versatile **Ctrl+C** [shortcut](https://www.howtouselinux.com/post/the-complete-google-gemini-cli-cheat-sheet-and-guide#:~:text=Shortcut%20Description%20,Press%20twice%20to%20confirm). This gives you immediate control when you need to stop or exit.

Gemini CLI operates like a REPL, and knowing how to break out of operations is essential. Pressing **Ctrl+C** once will cancel the current action or clear any input you've started typing, essentially acting as an "abort" [command](https://www.howtouselinux.com/post/the-complete-google-gemini-cli-cheat-sheet-and-guide#:~:text=Shortcut%20Description%20,Press%20twice%20to%20confirm). For example, if the AI is generating a lengthy answer and you've seen enough, hit `Ctrl+C` - the generation stops immediately. If you had started typing a prompt but want to discard it, `Ctrl+C` will wipe the input line so you can start [fresh](https://www.howtouselinux.com/post/the-complete-google-gemini-cli-cheat-sheet-and-guide#:~:text=Shortcut%20Description%20,Press%20twice%20to%20confirm). Additionally, if you are in **shell mode** (activated by typing `!` to run shell commands), a single `Ctrl+C` will exit shell mode and return you to the normal Gemini prompt (it sends an interrupt to the shell process [running](https://milvus.io/ai-quick-reference/how-do-i-use-gemini-cli-for-shell-command-generation#:~:text=The%20shell%20integration%20also%20includes,where%20you%20can%20generate%20commands). This is extremely handy if a shell command is hanging or you simply want to get back to AI mode.

Pressing **Ctrl+C twice** in a row is the shortcut to exit Gemini CLI [entirely](https://www.howtouselinux.com/post/the-complete-google-gemini-cli-cheat-sheet-and-guide#:~:text=Shortcut%20Description%20,Press%20twice%20to%20confirm). Think of it as "`Ctrl+C` to cancel, and `Ctrl+C` again to quit." This double-tap signals the CLI to terminate the session (you'll see a goodbye message or the program will close). It's a faster alternative to typing `/quit` or closing the terminal window, allowing you to gracefully shut down the CLI from the keyboard. Do note that a single `Ctrl+C` will not quit if there's input to clear or an operation to interrupt - it requires that second press (when the prompt is idle) to fully [exit](https://www.howtouselinux.com/post/the-complete-google-gemini-cli-cheat-sheet-and-guide#:~:text=Shortcut%20Description%20,Press%20twice%20to%20confirm). This design prevents accidentally closing the session when you only meant to stop the current output.

**Pro Tip:** In shell mode, you can also press the **Esc** key to leave shell mode and return to Gemini's chat mode without terminating the [CLI](https://milvus.io/ai-quick-reference/how-do-i-use-gemini-cli-for-shell-command-generation#:~:text=The%20shell%20integration%20also%20includes,where%20you%20can%20generate%20commands). And if you prefer a more formal exit, the `/quit` command is always available to cleanly end the session. Lastly, Unix users can use **Ctrl+D** (EOF) at an empty prompt to exit as well - Gemini CLI will prompt for confirmation if [needed](https://www.howtouselinux.com/post/the-complete-google-gemini-cli-cheat-sheet-and-guide#:~:text=Shortcut%20Description%20,Press%20twice%20to%20confirm). But for most cases, mastering the single- and double-tap of `Ctrl+C` is the quickest way to stay in control.

## Tip 23: Customize Gemini CLI with `settings.json`

**Quick use-case:** Adapt the CLI's behavior and appearance to your preferences or project conventions by editing the `settings.json` config file, instead of sticking with one-size-fits-all [defaults](https://www.philschmid.de/gemini-cli-cheatsheet#:~:text=%2A%20%60autoAccept%60%3A%20Auto,to%20disable%20usage%20statistics). This lets you enforce things like theme, tool usage rules, or editor mode across all your sessions.

Gemini CLI is highly configurable. In your home directory (`~/.gemini/`) or project folder (`.gemini/` within your repo), you can create a `settings.json` file to override default [settings](https://www.philschmid.de/gemini-cli-cheatsheet#:~:text=Customize%20the%20CLI%20by%20creating,applied%20with%20the%20following%20precedence). Nearly every aspect of the CLI can be tuned here - from visual theme to tool permissions. The CLI merges settings from multiple levels: system-wide defaults, your user settings, and project-specific settings (project settings override user [settings](https://www.philschmid.de/gemini-cli-cheatsheet#:~:text=Customize%20the%20CLI%20by%20creating,applied%20with%20the%20following%20precedence). For example, you might have a global preference for a dark theme, but a particular project might require stricter tool sandboxing; you can handle this via different `settings.json` files at each level.

Inside `settings.json`, options are specified as JSON key-value pairs. Here's a snippet illustrating some useful customizations:

```json
{
"theme": "GitHub",
"autoAccept": false,
"vimMode": true,
"sandbox": "docker",
"includeDirectories": ["../shared-library", "~/common-utils"],
"usageStatisticsEnabled": true
}
```

In this example, we set the theme to "GitHub" (a popular color scheme), disable `autoAccept` (so the CLI will always ask before running potentially altering tools), enable Vim keybindings for the input editor, and enforce using Docker for tool sandboxing. We also added some directories to the workspace context (`includeDirectories`) so Gemini can see code in shared paths by [default](https://www.philschmid.de/gemini-cli-cheatsheet#:~:text=%7B%20,utils). Finally, we kept `usageStatisticsEnabled` true to collect basic usage stats (which feeds into telemetry, if [enabled](https://www.philschmid.de/gemini-cli-cheatsheet#:~:text=%2A%20%60autoAccept%60%3A%20Auto,to%20disable%20usage%20statistics). There are many more settings available - like defining custom color themes, adjusting token limits, or whitelisting/blacklisting specific tools - all documented in the configuration [guide](https://www.philschmid.de/gemini-cli-cheatsheet#:~:text=%2A%20%60autoAccept%60%3A%20Auto,to%20disable%20usage%20statistics). By tailoring these, you ensure Gemini CLI behaves optimally for *your* workflow (for instance, some developers always want `vimMode` on for efficiency, while others might prefer the default editor).

One convenient way to edit settings is via the built-in settings UI. Run the command `/settings` in Gemini CLI, and it will open an interactive editor for your [configuration](https://google-gemini.github.io/gemini-cli/docs/cli/commands.html#:~:text=,their%20current%20values%2C%20and%20modify). This interface lets you browse and search settings with descriptions, and prevents JSON syntax errors by validating inputs. You can tweak colors, toggle features like `yolo` (auto-approval), adjust checkpointing (file save/restore behavior), and more through a friendly [menu](https://google-gemini.github.io/gemini-cli/docs/cli/commands.html#:~:text=,their%20current%20values%2C%20and%20modify). Changes are saved to your `settings.json`, and some take effect immediately (others might require restarting the CLI).

**Pro Tip:** Maintain separate project-specific `settings.json` files for different needs. For example, on a team project you might set `"sandbox": "docker"` and `"excludeTools": ["run_shell_command"]` to lock down dangerous operations, while your personal projects might allow direct shell commands. Gemini CLI will automatically pick up the nearest `.gemini/settings.json` in your project directory tree and merge it with your global [`~/.gemini/settings.json`](https://www.philschmid.de/gemini-cli-cheatsheet#:~:text=Customize%20the%20CLI%20by%20creating,applied%20with%20the%20following%20precedence). Also, don't forget you can quickly adjust visual preferences: try `/theme` to interactively switch themes without editing the file, which is great for finding a comfortable [look](https://www.howtouselinux.com/post/the-complete-google-gemini-cli-cheat-sheet-and-guide#:~:text=Command%20Description%20,tag%3E%60Save%20the%20current%20conversation). Once you find one, put it in `settings.json` to make it permanent.

## Tip 24: Leverage IDE Integration (VS Code) for Context & Diffs

**Quick use-case:** Supercharge Gemini CLI by hooking it into VS Code - the CLI will automatically know which files you're working on and even open AI-proposed code changes in VS Code's diff editor for [you](https://developers.googleblog.com/en/gemini-cli-vs-code-native-diffing-context-aware-workflows/?source=post_page-----26afd3422028---------------------------------------#:~:text=,working%20on%20at%20the%20moment). This creates a seamless loop between AI assistant and your coding workspace.

One of Gemini CLI's powerful features is its **IDE integration** with Visual Studio Code. By installing the official *Gemini CLI Companion* extension in VS Code and connecting it, you allow Gemini CLI to become "context-aware" of your [editor](https://developers.googleblog.com/en/gemini-cli-vs-code-native-diffing-context-aware-workflows/?source=post_page-----26afd3422028---------------------------------------#:~:text=,working%20on%20at%20the%20moment). What does this mean in practice? When connected, Gemini knows about the files you have open, your current cursor location, and any text you've selected in VS [Code](https://developers.googleblog.com/en/gemini-cli-vs-code-native-diffing-context-aware-workflows/?source=post_page-----26afd3422028---------------------------------------#:~:text=,working%20on%20at%20the%20moment). All that information is fed into the AI's context. So if you ask, "Explain this function," Gemini CLI can see the exact function you've highlighted and give a relevant answer, without you needing to copy-paste code into the prompt. The integration shares up to your 10 most recently opened files, plus selection and cursor info, giving the model a rich understanding of your [workspace](https://gemini-cli.xyz/docs/en/ide-integration#:~:text=,reject%20the%20suggested%20changes%20seamlessly).

Another huge benefit is **native diffing** of code changes. When Gemini CLI suggests modifications to your code (for example, "refactor this function" and it produces a patch), it can open those changes in VS Code's diff viewer [automatically](https://developers.googleblog.com/en/gemini-cli-vs-code-native-diffing-context-aware-workflows/?source=post_page-----26afd3422028---------------------------------------#:~:text=%2A%20Native%20in,the%20code%20right%20within%20this). You'll see a side-by-side diff in VS Code showing the proposed edits. You can then use VS Code's familiar interface to review the changes, make any manual tweaks, and even accept the patch with a click. The CLI and editor stay in sync - if you accept the diff in VS Code, Gemini CLI knows and continues the session with those changes applied. This tight loop means you no longer have to copy code from the terminal to your editor; the AI's suggestions flow straight into your development environment.

**How to set it up:** If you start Gemini CLI inside VS Code's integrated terminal, it will detect VS Code and usually prompt you to install/connect the extension [automatically](https://medium.com/google-cloud/gemini-cli-tutorial-series-part-10-gemini-cli-vs-code-integration-26afd3422028#:~:text=Press%20enter%20or%20click%20to,view%20image%20in%20full%20size). You can agree and it will run the necessary `/ide install` step. If you don't see a prompt (or you're enabling it later), simply open Gemini CLI and run the command: `/ide install`. This will fetch and install the "Gemini CLI Companion" extension into VS Code for [you](https://developers.googleblog.com/en/gemini-cli-vs-code-native-diffing-context-aware-workflows/?source=post_page-----26afd3422028---------------------------------------#:~:text=2%3A%20One,install%20the%20necessary%20companion%20extension). Next, run `/ide enable` to establish the [connection](https://developers.googleblog.com/en/gemini-cli-vs-code-native-diffing-context-aware-workflows/?source=post_page-----26afd3422028---------------------------------------#:~:text=3%3A%20Toggle%20integration%3A%20After%20the,can%20easily%20manage%20the%20integration) - the CLI will then indicate it's linked to VS Code. You can verify at any time with `/ide status`, which will show if it's connected and list which editor and files are being [tracked](https://gemini-cli.xyz/docs/en/ide-integration#:~:text=Checking%20the%20Status). From then on, Gemini CLI will automatically receive context from VS Code (open files, selections) and will open diffs in VS Code when needed. It essentially turns Gemini CLI into an AI pair programmer that lives in your terminal but operates with full awareness of your IDE.

Currently, VS Code is the primary supported editor for this [integration](https://gemini-cli.xyz/docs/en/ide-integration#:~:text=better%20and%20enables%20powerful%20features,editor%20diffing). (Other editors that support VS Code extensions, like VSCodium or some JetBrains via a plugin, may work via the same extension, but officially it's VS Code for now.) The design is open though - there's an IDE Companion Spec for developing similar integrations with other [editors](https://gemini-cli.xyz/docs/en/ide-integration#:~:text=better%20and%20enables%20powerful%20features,editor%20diffing). So down the road we might see first-class support for IDEs like IntelliJ or Vim via community extensions.

**Pro Tip:** Once connected, you can use VS Code's Command Palette to control Gemini CLI without leaving the [editor](https://gemini-cli.xyz/docs/en/ide-integration#:~:text=,Ctrl%2BShift%2BP). For example, press **Ctrl+Shift+P** (Cmd+Shift+P on Mac) and try commands like **"Gemini CLI: Run"** (to launch a new CLI session in the terminal), **"Gemini CLI: Accept Diff"** (to approve and apply an open diff), or **"Gemini CLI: Close Diff Editor"** (to reject [changes](https://gemini-cli.xyz/docs/en/ide-integration#:~:text=,Ctrl%2BShift%2BP). These shortcuts can streamline your workflow even further. And remember, you don't always have to start the CLI manually - if you enable the integration, Gemini CLI essentially becomes an AI co-developer inside VS Code, watching context and ready to help as you work on code.

## Tip 25: Automate Repo Tasks with `Gemini CLI GitHub Action`

**Quick use-case:** Put Gemini to work on GitHub - use the **Gemini CLI GitHub Action** to autonomously triage new issues and review pull requests in your repository, acting as an AI teammate that handles routine dev [tasks](https://blog.google/technology/developers/introducing-gemini-cli-github-actions/#:~:text=1,write%20tests%20for%20this).

Gemini CLI isn't just for interactive terminal sessions; it can also run in CI/CD pipelines via GitHub Actions. Google has provided a ready-made **Gemini CLI GitHub Action** (currently in beta) that integrates into your repo's [workflows](https://blog.google/technology/developers/introducing-gemini-cli-github-actions/#:~:text=It%E2%80%99s%20now%20in%20beta%2C%20available,cli). This effectively deploys an AI agent into your project on GitHub. It runs in the background, triggered by repository [events](https://blog.google/technology/developers/introducing-gemini-cli-github-actions/#:~:text=Triggered%20by%20events%20like%20new,do%2C%20and%20gets%20it%20done). For example, when someone opens a **new issue**, the Gemini Action can automatically analyze the issue description, apply relevant labels, and even prioritize it or suggest duplicates (this is the "intelligent issue triage" [workflow](https://blog.google/technology/developers/introducing-gemini-cli-github-actions/#:~:text=1,attention%20on%20what%20matters%20most). When a **pull request** is opened, the Action kicks in to provide an **AI code review** - it will comment on the PR with insights about code quality, potential bugs, or stylistic [improvements](https://blog.google/technology/developers/introducing-gemini-cli-github-actions/#:~:text=attention%20on%20what%20matters%20most,more%20complex%20tasks%20and%20decisions). This gives maintainers immediate feedback on the PR before any human even looks at it. Perhaps the coolest feature is **on-demand collaboration**: team members can mention `@gemini-cli` in an issue or PR comment and give it an instruction, like "`@gemini-cli` please write unit tests for this". The Action will pick that up and Gemini CLI will attempt to fulfill the request (adding a commit with new tests, for [instance](https://blog.google/technology/developers/introducing-gemini-cli-github-actions/#:~:text=freeing%20up%20reviewers%20to%20focus,write%20tests%20for%20this). It's like having an AI assistant living in your repo, ready to do chores when asked.

Setting up the Gemini CLI GitHub Action is straightforward. First, ensure you have Gemini CLI version **0.1.18 or later** installed locally (this ensures compatibility with the [Action](https://blog.google/technology/developers/introducing-gemini-cli-github-actions/#:~:text=Gemini%20CLI%20GitHub%20Actions%20is,for%20individual%20users%20available%20soon). Then, in Gemini CLI run the special command: [`/setup-github`](https://blog.google/technology/developers/introducing-gemini-cli-github-actions/#:~:text=To%20get%20started%2C%20download%20Gemini,cli). This command generates the necessary workflow files in your repository (it will guide you through authentication if needed). Specifically, it adds YAML workflow files (for issue triage, PR review, etc.) under `.github/workflows/`. You will need to add your Gemini API key to the repo's secrets (as `GEMINI_API_KEY`) so the Action can use the Gemini [API](https://github.com/google-github-actions/run-gemini-cli#:~:text=Store%20your%20API%20key%20as,in%20your%20repository). Once that's done and the workflows are committed, the GitHub Action springs to life - from that point on, Gemini CLI will autonomously respond to new issues and PRs according to those workflows.

Because this Action is essentially running Gemini CLI in an automated way, you can customize it just like you would your CLI. The default setup comes with three workflows (issue triage, PR review, and a general mention-triggered assistant) which are **fully open-source and [editable**](https://blog.google/technology/developers/introducing-gemini-cli-github-actions/#:~:text=Think%20of%20these%20initial%20workflows,into%20Gemini%20CLI%20GitHub%20Actions). You can tweak the YAML to adjust what the AI does, or even add new workflows. For instance, you might create a nightly workflow that uses Gemini CLI to scan your repository for outdated dependencies or to update a README based on recent code changes - the possibilities are endless. The key benefit here is offloading mundane or time-consuming tasks to an AI agent so that human developers can focus on harder problems. And since it runs on GitHub's infrastructure, it doesn't require your intervention - it's truly a "set and forget" AI helper.

**Pro Tip:** Keep an eye on the Action's output in the GitHub Actions logs for transparency. The Gemini CLI Action logs will show what prompts it ran and what changes it made or suggested. This can both build trust and help you refine its behavior. Also, the team has built enterprise-grade safeguards into the Action - e.g., you can require that all shell commands the AI tries to run in a workflow are allow-listed by [you](https://blog.google/technology/developers/introducing-gemini-cli-github-actions/#:~:text=in%20your%20environment%2C%20drastically%20reducing,your%20preferred%20observability%20platform%2C%20like). So don't hesitate to use it even on serious projects. And if you come up with a cool custom workflow using Gemini CLI, consider contributing it back to the community - the project welcomes new ideas in their repo!

## Tip 26: Enable Telemetry for Insights and Observability

**Quick use-case:** Gain deeper insight into how Gemini CLI is being used and performing by turning on its built-in **OpenTelemetry** instrumentation - monitor metrics, logs, and traces of your AI sessions to analyze usage patterns or troubleshoot [issues](https://google-gemini.github.io/gemini-cli/docs/cli/telemetry.html#:~:text=,across%20teams%2C%20track%20costs%2C%20ensure).

For developers who like to measure and optimize, Gemini CLI offers an observability feature that exposes what's happening under the hood. By leveraging **OpenTelemetry (OTEL)**, Gemini CLI can emit structured telemetry data about your [sessions](https://google-gemini.github.io/gemini-cli/docs/cli/telemetry.html#:~:text=Built%20on%20OpenTelemetry%20%E2%80%94%20the,Gemini%20CLI%E2%80%99s%20observability%20system%20provides). This includes things like metrics (e.g. how many tokens used, response latency), logs of actions taken, and even traces of tool calls. With telemetry enabled, you can answer questions like: *Which custom command do I use most often? How many times did the AI edit files in this project this week? What's the average response time when I ask the CLI to run tests?* Such data is invaluable for understanding usage patterns and [performance](https://google-gemini.github.io/gemini-cli/docs/cli/telemetry.html#:~:text=,across%20teams%2C%20track%20costs%2C%20ensure). Teams can use it to see how developers are interacting with the AI assistant and where bottlenecks might be.

By default, telemetry is **off** (Gemini respects privacy and performance). You can opt-in by setting `"telemetry.enabled": true` in your `settings.json` or by starting Gemini CLI with the flag [`--telemetry`](https://google-gemini.github.io/gemini-cli/docs/cli/telemetry.html#:~:text=Setting%20Environment%20Variable%20CLI%20Flag,grpc). Additionally, you choose the **target** for the telemetry data: it can be logged **locally** or sent to a backend like Google Cloud. For a quick start, you might set `"telemetry.target": "local"` - with this, Gemini will simply write telemetry data to a local file (by default) or to a custom path you specify via `["outfile"](https://google-gemini.github.io/gemini-cli/docs/cli/telemetry.html#:~:text=disable%20telemetry%20,file%20path)`. The local telemetry includes JSON logs you can parse or feed into tools. For more robust monitoring, set `"target": "gcp"` (Google Cloud) or even integrate with other OpenTelemetry-compatible systems like Jaeger or [Datadog](https://google-gemini.github.io/gemini-cli/docs/cli/telemetry.html#:~:text=,between%20backends%20without%20changing%20your). In fact, Gemini CLI's OTEL support is vendor-neutral - you can export data to just about any observability stack you prefer (Google Cloud Operations, Prometheus, [etc.](https://google-gemini.github.io/gemini-cli/docs/cli/telemetry.html#:~:text=,between%20backends%20without%20changing%20your). Google provides a streamlined path for Cloud: if you point to GCP, the CLI can send data directly to Cloud Logging and Cloud Monitoring in your project, where you can use the usual dashboards and alerting [tools](https://google-gemini.github.io/gemini-cli/docs/cli/telemetry.html#:~:text=2,explorer%20%2A%20Traces%3A%20https%3A%2F%2Fconsole.cloud.google.com%2Ftraces%2Flist).

What kind of insights can you get? The telemetry captures events like tool executions, errors, and important milestones. It also records metrics such as prompt processing time and token counts per [prompt](https://medium.com/google-cloud/gemini-cli-tutorial-series-part-13-gemini-cli-observability-c410806bc112#:~:text=,integrate%20with%20existing%20monitoring%20infrastructure). For usage analytics, you might aggregate how many times each slash command is used across your team, or how often code generation is invoked. For performance monitoring, you could track if responses have gotten slower, which might indicate hitting API rate limits or model changes. And for debugging, you can see errors or exceptions thrown by tools (e.g., a `run_shell_command` failure) logged with context. All this data can be visualized if you send it to a platform like Google Cloud's Monitoring - for example, you can create a dashboard of "tokens used per day" or "error rate of tool X". It essentially gives you a window into the AI's "brain" and your usage, which is especially helpful in enterprise settings to ensure everything runs [smoothly](https://medium.com/google-cloud/gemini-cli-tutorial-series-part-13-gemini-cli-observability-c410806bc112#:~:text=resource%20utilization%20%2A%20%20Real,integrate%20with%20existing%20monitoring%20infrastructure).

Enabling telemetry does introduce some overhead (extra data processing), so you might not keep it on 100% of the time for personal use. However, it's fantastic for debugging sessions or for intermittent health checks. One approach is to enable it on a CI server or in your team's shared environment to collect stats, while leaving it off locally unless needed. Remember, you can always toggle it on the fly: update settings and use `/memory refresh` if needed to reload, or restart Gemini CLI with `--telemetry` flag. Also, all telemetry is under your control - it respects your environment variables for endpoint and credentials, so data goes only where you intend it to. This feature turns Gemini CLI from a black box into an observatory, shining light on how the AI agent interacts with your world, so you can continuously improve that interaction.

**Pro Tip:** If you just want a quick view of your current session's stats (without full telemetry), use the `/stats` command. It will output metrics like token usage and session length right in the [CLI](https://www.howtouselinux.com/post/the-complete-google-gemini-cli-cheat-sheet-and-guide#:~:text=Command%20Description%20,tag%3E%60Save%20the%20current%20conversation). This is a lightweight way to see immediate numbers. But for long-term or multi-session analysis, telemetry is the way to go. And if you're sending telemetry to a cloud project, consider setting up dashboards or alerts (e.g., alert if error rate spikes or token usage hits a threshold) - this can proactively catch issues in how Gemini CLI is being used in your team.

## Tip 27: Keep an Eye on the Roadmap (Background Agents & More)

**Quick use-case:** Stay informed about upcoming Gemini CLI features - by following the public **Gemini CLI roadmap**, you'll know about major planned enhancements (like *background agents for long-running tasks*) before they [arrive](https://google-gemini.github.io/gemini-cli/ROADMAP.html#:~:text=quality.%20,related%20to%20security%20and%20privacy), allowing you to plan and give feedback.

Gemini CLI is evolving rapidly, with new releases coming out frequently, so it's wise to track what's on the horizon. Google maintains a **public roadmap** for Gemini CLI on GitHub, detailing the key focus areas and features targeted for the near [future](https://google-gemini.github.io/gemini-cli/ROADMAP.html#:~:text=This%20document%20outlines%20our%20approach,live%20in%20our%20GitHub%20Issues). This is essentially a living document (and set of issues) where you can see what the developers are working on and what's in the pipeline. For instance, one exciting item on the roadmap is support for **background agents** - the ability to spawn autonomous agents that run in the background to handle tasks continuously or [asynchronously](https://google-gemini.github.io/gemini-cli/ROADMAP.html#:~:text=quality.%20,related%20to%20security%20and%20privacy). According to the roadmap discussion, these background agents would let you delegate long-running processes to Gemini CLI without tying up your interactive session. You could, say, start a background agent that monitors your project for certain events or periodically executes tasks, either on your local machine or even by deploying to a service like Cloud [Run](https://github.com/google-gemini/gemini-cli/issues/4168#:~:text=How%20will%20it%20work%3F). This feature aims to "enable long-running, autonomous tasks and proactive assistance" right from the [CLI](https://google-gemini.github.io/gemini-cli/ROADMAP.html#:~:text=quality.%20,related%20to%20security%20and%20privacy), essentially extending Gemini CLI's usefulness beyond just on-demand queries.

By keeping tabs on the roadmap, you'll also learn about other planned features. These could include new tool integrations, support for additional Gemini model versions, UI/UX improvements, and more. The roadmap is usually organized by "areas" (for example, *Extensibility*, *Model*, *Background*, etc.) and often tagged with milestones (like a target quarter for [delivery](https://google-gemini.github.io/gemini-cli/ROADMAP.html#:~:text=Our%20roadmap%20is%20managed%20directly,more%20detailed%20list%20of%20tasks)\]. It's not a guarantee of when something will land, but it gives a good idea of the team's priorities. Since the project is open-source, you can even dive into the linked GitHub issues for each roadmap item to see design proposals and progress. For developers who rely on Gemini CLI, this transparency means you can anticipate changes - maybe an API is adding a feature you need, or a breaking change might be coming that you want to prepare for.

Following the roadmap can be as simple as bookmarking the GitHub project board or issue labeled "Roadmap" and checking periodically. Some major updates (like the introduction of Extensions or the IDE integration) were hinted at in the roadmap before they were officially announced, so you get a sneak peek. Additionally, the Gemini CLI team often encourages community feedback on those future features. If you have ideas or use cases for something like background agents, you can usually comment on the issue or discussion thread to influence its development.

**Pro Tip:** Since Gemini CLI is open source (Apache 2.0 licensed), you can do more than just watch the roadmap - you can participate! The maintainers welcome contributions, especially for items aligned with the [roadmap](https://google-gemini.github.io/gemini-cli/ROADMAP.html#:~:text=As%20an%20Apache%202,opening%20an%20issue%20for%20discussion). If there's a feature you really care about, consider contributing code or testing once it's in preview. At the very least, you can open a feature request if something you need isn't on the roadmap [yet](https://google-gemini.github.io/gemini-cli/ROADMAP.html#:~:text=As%20an%20Apache%202,opening%20an%20issue%20for%20discussion). The roadmap page itself provides guidance on how to propose changes. Engaging with the project not only keeps you in the loop but also lets you shape the tool that you use. After all, Gemini CLI is built with community involvement in mind, and many recent features (like certain extensions and tools) started as community suggestions.

## Tip 28: Extend Gemini CLI with `Extensions`

**Quick use-case:** Add new capabilities to Gemini CLI by installing plug-and-play **extensions** - for example, integrate with your favorite database or cloud service - expanding the AI's toolset without any heavy lifting on your [part](https://blog.google/technology/developers/gemini-cli-extensions/#:~:text=Gemini%20CLI%20is%20an%20open,design%20platforms%20to%20payment%20services). It's like installing apps for your CLI to teach it new tricks.

Extensions are a game-changer introduced in late 2025: they allow you to **customize and expand** Gemini CLI's functionality in a modular [way](https://blog.google/technology/developers/gemini-cli-extensions/#:~:text=Gemini%20CLI%20is%20an%20open,design%20platforms%20to%20payment%20services). An extension is essentially a bundle of configurations (and optionally code) that connects Gemini CLI to an external tool or service. For instance, Google released a suite of extensions for Google Cloud - there's one that helps deploy apps to Cloud Run, one for managing BigQuery, one for analyzing application security, and [more](https://blog.google/technology/developers/gemini-cli-extensions/#:~:text=In%20just%20three%20months%20since,source%20community). Partners and community developers have built extensions for all sorts of things: Dynatrace (monitoring), Elastic (search analytics), Figma (design assets), Shopify, Snyk (security scans), Stripe (payments), and the list is [growing](https://blog.google/technology/developers/gemini-cli-extensions/#:~:text=In%20just%20three%20months%20since,source%20community). By installing an appropriate extension, you instantly grant Gemini CLI the ability to use new domain-specific tools. The beauty is that these extensions come with a pre-defined **"playbook"** that teaches the AI how to use the new tools [effectively](https://blog.google/technology/developers/gemini-cli-extensions/#:~:text=Gemini%20CLI%20is%20an%20open,design%20platforms%20to%20payment%20services). That means once installed, you can ask Gemini CLI to perform tasks with those services and it will know the proper APIs or commands to invoke, as if it had that knowledge built-in.

Using extensions is very straightforward. The CLI has a command to manage them: `gemini extensions install <URL>`. Typically, you provide the URL of the extension's GitHub repo or a local path, and the CLI will fetch and install [it](https://blog.google/technology/developers/gemini-cli-extensions/#:~:text=It%E2%80%99s%20easy%20to%20install%20an,%E2%80%9D%20from%20your%20command%20line). For example, to install an official extension, you might run: `gemini extensions install https://github.com/google-gemini/gemini-cli-extension-cloud-run`. Within seconds, the extension is added to your environment (stored under `~/.gemini/extensions/` or your project's `.gemini/extensions/` folder). You can then see it by running `/extensions` in the CLI, which lists active [extensions](https://google-gemini.github.io/gemini-cli/docs/cli/commands.html#:~:text=,See%20Gemini%20CLI%20Extensions). From that point on, the AI has new tools at its disposal. If it's a Cloud Run extension, you could say "Deploy my app to Cloud Run," and Gemini CLI will actually be able to execute that (by calling the underlying `gcloud` commands through the extension's tools). Essentially, extensions function as first-class expansions of Gemini CLI's capabilities, but you opt-in to the ones you need.

There's an **open ecosystem** around extensions. Google has an official Extensions page listing available [extensions](https://blog.google/technology/developers/gemini-cli-extensions/#:~:text=Access%20an%20open%2C%20growing%20ecosystem,of%20partners%20and%20builders), and because the framework is open, anyone can create and share their own. If you have a particular internal API or workflow, you can build an extension for it so that Gemini CLI can assist with it. Writing an extension is easier than it sounds: you typically create a directory (say, `my-extension/`) with a file `gemini-extension.json` describing what tools or context to [add](https://www.philschmid.de/gemini-cli-cheatsheet#:~:text=Extensions). You might define new slash commands or specify remote APIs the AI can call. No need to modify Gemini CLI's core - just drop in your extension. The CLI is designed to load these at runtime. Many extensions consist of adding custom *MCP tools* (Model Context Protocol servers or functions) that the AI can use. For example, an extension could add a `/translate` command by hooking into an external translation API; once installed, the AI knows how to use `/translate`. The key benefit is **modularity**: you install only the extensions you want, keeping the CLI lightweight, but you have the option to integrate virtually anything.

To manage extensions, besides the `install` command, you can update or remove them via similar CLI commands (`gemini extensions update` or just by removing the folder). It's wise to occasionally check for updates on extensions you use, as they may receive improvements. The CLI might introduce an "extensions marketplace" style interface in the future, but for now, exploring the GitHub repositories and official catalog is the way to discover new ones. Some popular ones at launch include the GenAI **Genkit** extension (for building generative AI apps), and a variety of Google Cloud extensions that cover CI/CD, database admin, and more.

**Pro Tip:** If you're building your own extension, start by looking at existing ones for examples. The official documentation provides an **Extensions Guide** with the schema and [capabilities](https://www.philschmid.de/gemini-cli-cheatsheet#:~:text=Extensions). A simple way to create a private extension is to use the `@include` functionality in `GEMINI.md` to inject scripts or context, but a full extension gives you more power (like packaging tools). Also, since extensions can include context files, you can use them to preload domain knowledge. Imagine an extension for your company's internal API that includes a summary of the API and a tool to call it - the AI would then know how to handle requests related to that API. In short, extensions open up a new world where Gemini CLI can interface with anything. Keep an eye on the extensions marketplace for new additions, and don't hesitate to share any useful extension you create with the community - you might just help thousands of other [developers](https://blog.google/technology/developers/gemini-cli-extensions/#:~:text=Gemini%20CLI%20extensions%20are%20here,and%20build%20your%20own%20extension).

## Additional Fun: Corgi Mode Easter Egg 🐕

Lastly, not a productivity tip but a delightful easter egg - try the command `*/corgi*` in Gemini CLI. This toggles **"corgi mode"**, which makes a cute corgi animation run across your [terminal](https://medium.com/@ferreradaniel/gemini-cli-free-ai-tool-upgrade-5-new-features-you-need-right-now-04cfefac5e93#:~:text=Easter%20Egg%3A%20Corgi%20Mode%20in,Gemini%20CLI)! It doesn't help you code any better, but it can certainly lighten the mood during a long coding session. You'll see an ASCII art corgi dashing in the CLI interface. To turn it off, just run `/corgi` again.

This is a purely for-fun feature the team added (and yes, there's even a tongue-in-cheek [debate](https://github.com/google-gemini/gemini-cli/issues/5674#:~:text=How%20about%20you%20NOT%20implement,this%20needed%3F%20Because%20people) about spending dev time on corgi mode). It shows that the creators hide some whimsy in the tool. So when you need a quick break or a smile, give `/corgi` a try. 🐕🎉

*(Rumor has it there might be other easter eggs or modes - who knows? Perhaps a "/partyparrot" or similar. The cheat sheet or help command lists `/corgi`, so it's not a secret, just underused. Now you're in on the joke!)*

---

**Conclusion:**

We've covered a comprehensive list of pro tips and features for Gemini CLI. From setting up persistent context with `GEMINI.md`, to writing custom commands and using advanced tools like MCP servers, to leveraging multi-modal inputs and automating workflows, there's a lot this AI command-line assistant can do. As an external developer, you can integrate Gemini CLI into your daily routine - it's like a powerful ally in your terminal that can handle tedious tasks, provide insights, and even troubleshoot your environment.

Gemini CLI is evolving rapidly (being open-source with community contributions), so new features and improvements are constantly on the horizon. By mastering the pro tips in this guide, you'll be well-positioned to harness the full potential of this tool. It's not just about using an AI model - it's about integrating AI deeply into how you develop and manage software.

Happy coding with Gemini CLI, and have fun exploring just how far your "AI agent in the terminal" can take you.

**You now have a Swiss-army knife of AI at your fingertips - use it wisely, and it will make you a more productive (and perhaps happier) developer**!
