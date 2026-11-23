# git-diff-review
一个只做审查、不修改代码的 Git diff CLI。输入标准 unified diff，输出固定四个章节的 Markdown 审查清单：  - 变更摘要 - 风险点 - 建议测试 - 需人工确认项  默认通过 OpenAI 兼容的 Chat Completions 接口调用 LLM；也提供完全离线的规则后端，用于无密钥演示、结构测试和回归基线。
