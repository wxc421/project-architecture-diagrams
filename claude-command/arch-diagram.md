---
description: 为项目生成 6 张 Mermaid 架构图 HTML（Pipeline / 分层 / 构建 / 检索 / ER / 时序）。自动检测 Go/Java/Python/Node 项目类型。
---

# Architecture Diagrams

为当前项目生成 6 张 Mermaid 架构图的 HTML 文件，打开浏览器预览。

## 执行步骤

### 1. 检测项目类型

用 `ls` 检查特征文件：

- `go.mod` → Go，入口在 `cmd/` 或 `main.go`
- `pom.xml` / `build.gradle` → Java，入口在 `src/main/java/...Application.java`
- `package.json` → Node，入口在 `src/` 或入口 js
- `requirements.txt` / `pyproject.toml` → Python，入口在 `main.py` 或 `app.py`

### 2. 通读源码

按项目类型读关键文件：

**Go：** `cmd/server/main.go`、`internal/service/`、`internal/agent/`、各分层
**Java/Spring：** `controller/` → `service/` → `repository/mapper/` → `entity/`
**Python：** `main.py` → `services/` → `models/`
**Node：** `app.js` → `routes/` → `services/`

用 Bash read + Glob 工具。先读入口，再读核心业务。

### 3. 生成 6 张图的 Mermaid DSL

**图 1 — Pipeline（`graph TD`）：** 核心流程线性展开。用项目真实节点名。条件分支用菱形节点。

```
graph TD
  START((输入)):::start --> A[节点名<br/>说明]
  A --> B
  B{条件}:::gate
  B -->|通过| C[...]:::exec
  B -->|失败| D[...]:::error
  D --> C --> RESULT((结果))
```

**图 2 — 分层架构（`graph TD`）：** 每层一个宽盒子 node，6 条单箭头。

```
CMD["层名\n─────────────\n· 组件1\n· 组件2"]:::cmd
SVC["..."]:::svc
CMD -->|调用| SVC
```

**图 3 — 离线构建（`graph TD`）：** 数据/索引构建流程，含子图
**图 4 — 在线检索（`graph TD`）：** 运行时查找流程
**图 5 — ER 图（`erDiagram`）：** 用实际实体和字段
**图 6 — 时序图（`sequenceDiagram`）：** ≤6 参与方，用 `par` + `alt`

### 简化规则

- 不嵌套 subgraph
- 并行分流用 `PAR[...]:::branch` 中间节点，不用 `A & B & C --> D`
- 分层图不用 subgraph，用单个宽盒子 node
- 时序图 ≤6 participant
- 每个图用不同 classDef 颜色

### 4. 组装 HTML

用以下 HTML 壳，把 6 张图的 Mermaid DSL 插入到 `<!-- DIAGRAM TEMPLATE -->` 位置：

```html
<!DOCTYPE html>
<html lang="zh-CN">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>项目 — 架构图</title>
<link href="https://fonts.googleapis.com/css2?family=Inter:wght@400;500;600;700&display=swap" rel="stylesheet">
<style>
:root {
  --font-body: 'Inter', ui-sans-serif, system-ui, sans-serif;
  --font-mono: 'JetBrains Mono', 'SF Mono', 'Fira Code', ui-monospace, monospace;
  --bg: oklch(96.7% 0.001 286.4);
  --surface: oklch(99% 0.002 286.4);
  --surface-muted: oklch(91.9% 0.004 286.3);
  --text: oklch(27.4% 0.006 286.0);
  --text-secondary: oklch(44.2% 0.017 285.8);
  --text-muted: #a1a1aa;
  --success: #22c55e;
  --error: #ef4444;
  --info: #3b82f6;
  --border: oklch(91.9% 0.004 286.3);
  --shadow-sm: 0 1px 2px rgba(0,0,0,0.05);
  --radius: 8px;
}
@media (prefers-color-scheme: dark) {
  :root {
    --bg: oklch(27.4% 0.006 286.0);
    --surface: oklch(37% 0.013 285.8);
    --text: oklch(96.7% 0.001 286.4);
    --text-secondary: #a1a1aa;
    --border: oklch(44.2% 0.017 285.8);
  }
}
body{font-family:var(--font-body);font-size:15px;line-height:1.6;color:var(--text);background:var(--bg);background-image:radial-gradient(circle,var(--border)1px,transparent 1px);background-size:24px 24px;padding:40px 20px}
.container{max-width:1100px;margin:0 auto}
h1{font-size:36px;font-weight:700;margin-bottom:6px}
h2{font-size:20px;font-weight:600;margin:48px 0 12px}
.mermaid-wrap{background:var(--surface);border:1px solid var(--border);border-radius:12px;padding:28px 20px;overflow:auto;margin-bottom:16px;box-shadow:var(--shadow-sm)}
.mermaid-wrap .mermaid{display:flex;justify-content:center}
.legend{display:flex;gap:18px;flex-wrap:wrap;margin-bottom:8px}
.legend-item{display:flex;align-items:center;gap:6px;font-family:var(--font-mono);font-size:11px;color:var(--text-muted)}
.legend-swatch{width:12px;height:12px;border-radius:3px}
</style>
</head>
<body>
<div class="container">
<h1>项目架构全景</h1>
<!-- 6 张图按以下结构插入 -->
<h2>图 1: Pipeline 数据流</h2>
<div class="mermaid-wrap"><div class="mermaid">...</div></div>
<div class="legend">...</div>
<!-- ... 图2-图6 ... -->
</div>
<script src="https://cdn.jsdelivr.net/npm/mermaid@10/dist/mermaid.min.js"></script>
<script>mermaid.initialize({startOnLoad:true,theme:'base'})</script>
</body>
</html>
```

保存到 `~/.agent/diagrams/[项目名]-architecture.html`。

### 5. 验证

用 Bash run open 打开文件，检查无 JS 错误。

### 6. 汇报

告诉用户文件路径和 6 张图的说明。
