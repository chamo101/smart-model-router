# Smart Model Router

> Claude Code 智能模型路由 Skill - 根据任务阶段自动切换模型，节省 90%+ API 成本

## 功能特点

- **智能路由**：规划阶段使用 Claude 高级模型，开发阶段自动切换到 Gemini 经济模型
- **成本优化**：日常开发使用 gemini-3-flash，大幅降低 API 费用
- **灵活切换**：支持手动 `/model` 命令随时切换模型
- **多模型支持**：覆盖 Claude 全系列 + Gemini 全系列模型

## 支持的模型

| 模型 | 类型 | 适用场景 |
|------|------|----------|
| `claude-sonnet-4-5` | Claude 高级 | 规划、架构设计、复杂推理 |
| `claude-sonnet-4-5-thinking` | Claude 深度思考 | 超复杂问题、深度分析 |
| `claude-opus-4-5-thinking` | Claude 顶级 | 最高质量要求、关键决策 |
| `gemini-3-flash` | Gemini 快速 | 日常开发、快速迭代 |
| `gemini-3-pro-high` | Gemini 高质量 | 复杂开发、代码审查 |
| `gemini-3-pro-low` | Gemini 经济 | 简单任务、批量处理 |
| `gemini-3-pro-image` | Gemini 图像 | 图像相关任务 |

## 安装

将此 Skill 放入 Claude Code 的 commands 目录：

```bash
# 克隆到 Claude Code commands 目录
git clone https://github.com/chamo101/smart-model-router.git ~/.claude/commands/smart-model-router
```

## 使用方式

### 自动路由策略

| 阶段 | 任务类型 | 推荐模型 |
|------|----------|----------|
| **规划阶段** | 架构设计、需求分析 | claude-sonnet-4-5 |
| **开发阶段** | 写代码、修 Bug、重构 | gemini-3-flash |
| **复杂开发** | 复杂算法、性能优化 | gemini-3-pro-high |
| **最终审查** | 代码审查、安全审计 | claude-opus-4-5-thinking |

### 手动切换

```bash
/model gemini-3-flash          # 日常开发（最常用）
/model gemini-3-pro-high       # 复杂开发
/model claude-sonnet-4-5       # 规划阶段
```

## 本地代理方案

支持多种本地 API 代理配置：

- **LiteLLM** - 最成熟的 LLM 网关，支持 100+ 模型
- **claude-code-proxy** - 专为 Claude Code 设计
- **Portkey Gateway** - 智能路由，200+ 模型支持

详细配置请查看 [SKILL.md](./SKILL.md)

## 成本节省

使用 `gemini-3-flash` 作为开发阶段默认模型，相比全程使用 Claude，可节省 **90%+** 的 API 成本。

## 最佳实践

1. **规划先行**：用高级模型做规划，确定方案
2. **批量开发**：进入开发后切换经济模型
3. **关键检查**：完成后切回高级模型做代码审查
4. **灵活调整**：遇到复杂问题随时切换到更强模型

## 相关项目

- [LiteLLM](https://github.com/BerriAI/litellm) - LLM 网关
- [claude-code-proxy](https://github.com/1rgs/claude-code-proxy) - Claude Code 代理
- [Portkey Gateway](https://github.com/Portkey-AI/gateway) - AI 网关

## License

MIT
