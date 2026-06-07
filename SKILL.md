---
name: project-architecture-diagrams
description: 为任意项目生成 6 张 Mermaid 架构图（pipeline / 分层 / 构建 / 检索 / ER / 时序），自动适配 Go/Java/Python/Node。说"画架构图"触发。
version: 1.0.0
---

# Project Architecture Diagrams

为**任意编程项目**生成 6 张技术架构图的 HTML 文件。自动检测项目类型（Go / Java / Python / Node），按该生态的惯例读取源码、生成对应架构图。

## 6 张图模板

| # | 标题 | Mermaid 类型 | 内容（按项目适配） |
|---|------|-------------|-------------------|
| 1 | Pipeline 数据流 | `graph TD` | 核心处理流程，用项目实际节点名 |
| 2 | 分层架构 | `graph TD` | 每层一个宽盒子（如 Controller/Service/Repository） |
| 3 | 离线构建（如有） | `graph TD` | 数据/索引/模型等构建流程 |
| 4 | 在线检索/查询（如有） | `graph TD` | 运行时数据查找流程 |
| 5 | 数据模型 ER 图 | `erDiagram` | 核心实体及其字段关系 |
| 6 | 请求时序图 | `sequenceDiagram` | 一次完整调用链，≤6 参与方 |

## 触发

用户说"画架构图"、"arch diagram"、"arch"、"生成项目文档图"时执行。

## 执行步骤

### 1. 检测项目类型

```bash
ls *
```

按特征文件判断：

| 文件 | 类型 | 典型入口 |
|------|------|---------|
| `go.mod` | Go | `cmd/` 或 `main.go` |
| `pom.xml` / `build.gradle` | Java | `src/main/java/...Application.java` |
| `package.json` | Node | `src/` 或入口 js |
| `requirements.txt` / `pyproject.toml` | Python | `main.py` 或 `app.py` |
| `Cargo.toml` | Rust | `src/main.rs` |

### 2. 通读源码（按类型适配）

**Go 项目：** `cmd/` → `internal/service/` → `internal/agent/` → 各分层包
**Java/Spring：** `controller/` → `service/` → `repository/mapper/` → `entity/`
**Python：** `main.py` → `services/` → `models/` → `db/repositories/`
**Node：** `app.js` → `routes/` → `services/` → `models/`

用 `mcp_codegraph_codegraph_context` 获取全景（如有 CodeGraph），或直接用 `search_files` + `read_file` 手动收集关键文件。

### 3. 构造 Mermaid DSL

**通用规则（适用所有项目类型）：**

1. **图 1 Pipeline** — `graph TD`，线性流程 + 条件分支，用项目真实函数/方法名
2. **图 2 分层架构** — `graph TD`，每层一个 node 宽盒子

   ```
   BOX["层名\n─────────────\n· 组件1\n· 组件2"]:::class
   ```

   每层之间 1 条箭头带标注，中间不要交叉线。

3. **图 3/4 构建+检索** — `graph TD`，项目特有的离线流程和在线查找
4. **图 5 ER 图** — `erDiagram`，用实际实体名和字段
5. **图 6 时序图** — `sequenceDiagram`，≤6 参与方，`par` 并行 + `alt` 分支

**简化防乱：**
- 不要嵌套 subgraph
- 并行分流用中间路由节点，不用 `A & B & C --> D`
- 时序图合并细粒度组件
- 每条 classDef 用不同颜色

### 4. 组装 HTML

加载模板 HTML 壳：
```
skill_view("project-architecture-diagrams", "templates/project-architecture.html")
```

把 6 张图的 Mermaid DSL 插入到 `<!-- DIAGRAM TEMPLATE -->` 位置。

保存到 `~/.agent/diagrams/[project-name]-architecture.html`。

### 5. 验证

```bash
open ~/.agent/diagrams/[project-name]-architecture.html
```

`browser_navigate` 确认页面加载，`browser_console` 确认 6 张 SVG 无 JS 错误。

### 6. 汇报

输出文件路径 + 6 张图简要说明。
