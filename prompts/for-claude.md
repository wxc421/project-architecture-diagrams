# Claude 使用指南

把这个 prompt 贴给 Claude，配合你的项目源码即可生成 6 张架构图。

---

## Prompt

请为我的项目生成 6 张 Mermaid 架构图，输出为一个自包含的 HTML 文件。

### 项目类型

[Go / Java / Python / Node / Rust — 选填]

### 项目源码关键文件

[贴上入口文件、核心分层、实体定义等源码]

### 6 张图要求

| # | 标题 | 类型 | 内容 |
|---|------|------|------|
| 1 | Pipeline 数据流 | `graph TD` | 核心处理流程，用实际函数/方法名，含条件分支 |
| 2 | 分层架构 & 依赖 | `graph TD` | 每层一个宽盒子，6 条单箭头带标注（如 Controller → Service → Repository → External） |
| 3 | 离线构建（如有） | `graph TD` | 数据/索引等构建流程 |
| 4 | 在线检索/查询（如有） | `graph TD` | 运行时数据查找流程 |
| 5 | 数据模型 ER 图 | `erDiagram` | 核心实体及其字段、关系 |
| 6 | 请求时序图 | `sequenceDiagram` | 一次完整调用链，≤6 参与方，用 par 和 alt |

### 简化规则

1. 不要嵌套 subgraph
2. 并行分流用中间路由节点，不要用 `A & B & C --> D`
3. 分层图每层 1 个盒子，用 `\n─────────────\n· 组件1\n· 组件2` 写内部分隔
4. 时序图 ≤6 个 participant
5. 每个图用不同颜色的 classDef
6. 用项目中真实的类名、函数名、实体名

### HTML 壳要求

```html
<h1>项目架构全景</h1>

<!-- 每个图的结构 -->
<h2>图 N: 标题</h2>
<div class="mermaid-wrap">
  <div class="mermaid">
    ... Mermaid DSL ...
  </div>
</div>
<div class="legend">...图例...</div>

<!-- 底部加载 Mermaid -->
<script src="https://cdn.jsdelivr.net/npm/mermaid@10/dist/mermaid.min.js"></script>
<script>
mermaid.initialize({ startOnLoad: true, theme: 'base' });
</script>
```

包含 CSS（dot-grid 背景、圆角卡片、深色/浅色适配、Inter 字体）。
