# Project Architecture Diagrams

为任意编程项目自动生成 6 张 Mermaid 架构图，保存为自包含 HTML 文件。

**支持项目类型：** Go / Java Spring Boot / Python FastAPI / Node.js / Rust

## 使用方法

### 方式一：Hermes Agent 中使用

1. 安装这个 skill：

```bash
mkdir -p ~/.hermes/skills/software-development/project-architecture-diagrams/{templates,prompts}
cp SKILL.md ~/.hermes/skills/software-development/project-architecture-diagrams/
cp templates/project-architecture.html ~/.hermes/skills/software-development/project-architecture-diagrams/templates/
```

2. 在项目目录下输入：

```
画架构图
```

Hermes 会自动检测项目类型、通读源码、生成 6 张图并打开浏览器预览。

### 方式二：Claude Code 中使用

安装 slash 命令：

```bash
curl -sL https://raw.githubusercontent.com/wxc421/project-architecture-diagrams/main/claude-command/arch-diagram.md \
  -o ~/.claude/commands/arch-diagram.md
```

然后在任意项目目录下输入 `/arch-diagram`，Claude Code 会自动检测项目类型、生成 6 张图并打开预览。

### 方式三：Claude.ai / 其他 AI 中使用

把 `prompts/for-claude.md` 的内容 + 你的项目源码发给 Claude 即可。

## 6 张图

| # | 标题 | Mermaid 类型 | 内容 |
|---|------|-------------|------|
| 1 | Pipeline 数据流 | `graph TD` | 核心流程：输入→处理→条件分支→输出 |
| 2 | 分层架构 & 依赖 | `graph TD` | 代码分层（Controller/Service/Repository → 外部服务），每层一个宽盒子 |
| 3 | 离线构建（如有） | `graph TD` | 索引/数据/模型等构建流程 |
| 4 | 数据模型 ER 图 | `erDiagram` | 核心实体关系 |
| 5 | 一次请求时序 | `sequenceDiagram` | 完整调用链，≤6 参与方 |

## 生成效果示例

打开 `examples/eino-data-agent-architecture.html` 查看实际生成的架构图（Go + Eino Text-to-SQL Agent）。

## 技术栈

- [Mermaid.js](https://mermaid.js.org/) — 图表渲染引擎
- 纯 HTML/CSS — 无外部依赖，离线可用（CDN 加载 Mermaid 库）
- 自动适配项目类型和语言生态

## License

MIT
