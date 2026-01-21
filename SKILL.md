---
name: smart-model-router
description: |
  智能模型路由策略：规划阶段使用原生高级模型(Claude/GPT-4)，开发细节时自动切换到经济模型(gemini-3-flash/gemini-3-pro-high)节省API成本。
  触发条件：开始新任务规划、进入代码实现阶段、或需要调整模型策略时
---

# Smart Model Router - 智能模型路由

## 可用模型列表

| 模型 | 类型 | 适用场景 |
|------|------|----------|
| `claude-sonnet-4-5` | Claude高级 | 规划、架构设计、复杂推理 |
| `claude-sonnet-4-5-thinking` | Claude深度思考 | 超复杂问题、深度分析 |
| `claude-opus-4-5-thinking` | Claude顶级 | 最高质量要求、关键决策 |
| `gemini-3-flash` | Gemini快速 | 日常开发、快速迭代 |
| `gemini-3-pro-high` | Gemini高质量 | 复杂开发、代码审查 |
| `gemini-3-pro-low` | Gemini经济 | 简单任务、批量处理 |
| `gemini-3-pro-image` | Gemini图像 | 图像相关任务 |

## 核心策略

| 阶段 | 任务类型 | 推荐模型 | 原因 |
|------|----------|----------|------|
| **规划阶段** | 架构设计、需求分析、方案评估 | claude-sonnet-4-5 | 需要强推理能力 |
| **深度规划** | 超复杂系统、关键架构决策 | claude-sonnet-4-5-thinking | 深度思考链 |
| **开发阶段** | 写代码、修Bug、重构、测试 | gemini-3-flash | 快速、经济 |
| **复杂开发** | 复杂算法、性能优化、安全相关 | gemini-3-pro-high | 平衡质量与成本 |
| **简单任务** | 格式化、简单修改、配置调整 | gemini-3-pro-low | 最经济 |
| **图像任务** | UI截图分析、图表理解 | gemini-3-pro-image | 多模态支持 |
| **最终审查** | 代码审查、安全审计、上线前检查 | claude-opus-4-5-thinking | 最高质量保证 |

## 自动识别规则

### 规划阶段特征（使用高级模型）
- 用户说：设计、规划、架构、方案、评估、分析、对比
- 任务涉及：系统设计、技术选型、需求梳理、风险评估
- 输出：文档、图表、决策建议

### 开发阶段特征（切换经济模型）
- 用户说：写、实现、修改、添加、修复、重构
- 任务涉及：具体代码编写、单元测试、样式调整、配置修改
- 输出：代码文件、测试用例、配置文件

## 本地API反代配置

### 方案1：LiteLLM（推荐）

最成熟的LLM网关，支持100+模型统一调用：

```yaml
# litellm_config.yaml
model_list:
  # Claude高级模型 - 规划阶段
  - model_name: claude-sonnet-4-5
    litellm_params:
      model: claude-sonnet-4-5
      api_key: ${ANTHROPIC_API_KEY}

  - model_name: claude-sonnet-4-5-thinking
    litellm_params:
      model: claude-sonnet-4-5-thinking
      api_key: ${ANTHROPIC_API_KEY}

  - model_name: claude-opus-4-5-thinking
    litellm_params:
      model: claude-opus-4-5-thinking
      api_key: ${ANTHROPIC_API_KEY}

  # Gemini经济模型 - 开发阶段
  - model_name: gemini-3-flash
    litellm_params:
      model: gemini-3-flash
      api_key: ${GOOGLE_API_KEY}

  - model_name: gemini-3-pro-high
    litellm_params:
      model: gemini-3-pro-high
      api_key: ${GOOGLE_API_KEY}

  - model_name: gemini-3-pro-low
    litellm_params:
      model: gemini-3-pro-low
      api_key: ${GOOGLE_API_KEY}

  - model_name: gemini-3-pro-image
    litellm_params:
      model: gemini-3-pro-image
      api_key: ${GOOGLE_API_KEY}

router_settings:
  routing_strategy: "cost-based"  # 或 "latency-based"
```

启动：
```bash
pip install litellm
litellm --config litellm_config.yaml --port 4000
```

### 方案2：claude-code-proxy

专为Claude Code设计的代理，支持Gemini/OpenAI后端：

```bash
git clone https://github.com/1rgs/claude-code-proxy
cd claude-code-proxy

# 配置模型映射
export OPENAI_API_KEY=your-key
export GOOGLE_API_KEY=your-key

# 运行代理
python proxy.py
```

配置Claude Code使用代理：
```bash
export ANTHROPIC_BASE_URL=http://localhost:8080
```

### 方案3：Portkey AI Gateway

支持200+模型的智能路由：

```javascript
// portkey-config.js
{
  "strategy": {
    "mode": "conditional",
    "conditions": [
      {
        "query_contains": ["设计", "规划", "架构", "分析"],
        "then": "claude-sonnet-4-5"
      },
      {
        "query_contains": ["深度思考", "复杂决策", "关键"],
        "then": "claude-sonnet-4-5-thinking"
      },
      {
        "query_contains": ["审查", "审计", "上线"],
        "then": "claude-opus-4-5-thinking"
      },
      {
        "query_contains": ["图片", "截图", "UI"],
        "then": "gemini-3-pro-image"
      },
      {
        "default": "gemini-3-flash"
      }
    ]
  }
}
```

## 手动切换命令

在对话中随时切换：

```
# Gemini经济模型
/model gemini-3-flash          # 日常开发（最常用）
/model gemini-3-pro-high       # 复杂开发
/model gemini-3-pro-low        # 简单任务
/model gemini-3-pro-image      # 图像任务

# Claude高级模型
/model claude-sonnet-4-5           # 规划阶段
/model claude-sonnet-4-5-thinking  # 深度思考
/model claude-opus-4-5-thinking    # 最高质量
```

## 成本对比参考

| 模型 | 类型 | 成本级别 | 适用场景 |
|------|------|----------|----------|
| `claude-opus-4-5-thinking` | 顶级 | $$$$$ | 最终审查、关键决策 |
| `claude-sonnet-4-5-thinking` | 高级思考 | $$$$ | 深度规划、复杂分析 |
| `claude-sonnet-4-5` | 高级 | $$$ | 规划、架构设计 |
| `gemini-3-pro-high` | 中高 | $$ | 复杂开发、代码审查 |
| `gemini-3-pro-low` | 经济 | $ | 简单任务、批量处理 |
| `gemini-3-flash` | 快速 | $ | 日常开发（推荐默认）|
| `gemini-3-pro-image` | 图像 | $$ | UI/图表分析 |

**节省策略**：开发阶段默认用 `gemini-3-flash`，可节省 **90%+** 成本

## 最佳实践

1. **规划先行**：每个任务先用高级模型做规划，确定方案
2. **批量开发**：进入开发后切换经济模型，批量完成实现
3. **关键检查**：完成后可切回高级模型做代码审查
4. **灵活调整**：遇到复杂问题随时切换到更强模型

## GitHub优质方案

| 项目 | Stars | 特点 |
|------|-------|------|
| [LiteLLM](https://github.com/BerriAI/litellm) | 15k+ | 最全面的LLM网关，支持100+模型 |
| [claude-code-proxy](https://github.com/1rgs/claude-code-proxy) | - | 专为Claude Code设计 |
| [Portkey Gateway](https://github.com/Portkey-AI/gateway) | 6k+ | 智能路由，200+模型支持 |
| [OpenRouter](https://openrouter.ai) | - | 托管服务，统一API |

---

Sources:
- [LiteLLM - GitHub](https://github.com/BerriAI/litellm)
- [claude-code-proxy - GitHub](https://github.com/1rgs/claude-code-proxy)
- [Portkey AI Gateway - GitHub](https://github.com/Portkey-AI/gateway)
