# Hermes Agent 安装

把这个项目安装为 Hermes skill：

```bash
SKILL_DIR=~/.hermes/skills/software-development/project-architecture-diagrams
mkdir -p $SKILL_DIR/templates $SKILL_DIR/prompts
cp SKILL.md $SKILL_DIR/
cp templates/project-architecture.html $SKILL_DIR/templates/
```

然后在新项目目录下输入 `画架构图` 即可自动触发。
