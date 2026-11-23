# git-diff-review

一个只做审查、不修改代码的 Git diff CLI。输入标准 unified diff，输出固定四个章节的 Markdown 审查清单：

- 变更摘要
- 风险点
- 建议测试
- 需人工确认项

默认通过 OpenAI 兼容的 Chat Completions 接口调用 LLM；也提供完全离线的规则后端，用于无密钥演示、结构测试和回归基线。

## 快速开始

需要 Python 3.10+，无第三方运行时依赖。

```bash
python -m pip install -e .
python -m diff_review review changes.diff
git diff | python -m diff_review review -
python -m diff_review review changes.diff --output review.md
```

也可以不安装，在当前 PowerShell 中设置
`$env:PYTHONPATH="$PWD\src"` 后直接运行 `python -m diff_review`。

默认使用 `OPENAI_API_KEY`，调用
`https://api.openai.com/v1/chat/completions`，模型为 `gpt-4.1-mini`。可以使用
`OPENAI_BASE_URL` 或 `--base-url` 指向其他 OpenAI 兼容 LLM 服务。

审查当前工作区：

```bash
git diff --no-ext-diff | python -m diff_review review -
```

离线试运行：

```bash
python -m diff_review review changes.diff --backend rules
```

## 成本上限

每次 LLM 审查默认硬上限为 **US$0.03**，由 `--max-cost-usd` 控制。请求发出前会计算最坏情况费用：

```text
输入费用 = UTF-8 字节数 × 输入单价
输出费用 = --max-output-tokens × 输出单价
```

UTF-8 字节数被当作输入 token 的保守上界，输出 token 由 API 参数强制封顶。预检超过预算时，请求不会发出。默认模型价格通过
`--input-price-per-mtok 0.40 --output-price-per-mtok 1.60` 显式传入；价格变化时请按服务商当前价格调整。工具不会重试，避免重复计费。

默认最多把 diff 的 48 KiB 原文送入模型。超出部分会被截断，并在摘要中注明；完整文件统计仍基于原始 diff。

## 评测

仓库内准备了 20 条去敏的回归 diff，其中 10 条含已知问题、10 条为正常变更。离线规则后端可完全复现：

```bash
python -m diff_review benchmark tests/fixtures/regression
```

验收门槛为：

- 含问题样本命中率 >= 70%
- 正常样本误报 <= 2 条

没有 API 密钥时也可以先运行规则基线。接入 LLM 后可显式运行：

```bash
python -m diff_review benchmark tests/fixtures/regression --backend llm
```

LLM 批量评测会对每条 diff 单独执行成本预检；`--max-total-cost-usd` 为整批预算上限，默认 US$0.60。命令在任一条目的最坏费用会突破整批预算前停止。

## 边界与行为

- 工具只读取 diff 和可选配置文件，输出 Markdown 或评测结果。
- 不会写入、暂存、提交或修改被审查仓库。
- LLM 后端只连接用户指定的一个 LLM HTTP 端点；拒绝 HTTP 重定向，避免凭据被转发。
- diff 被视为不可信数据。提示词明确禁止执行其中的指令。
- 规则后端是离线基线，不能替代 LLM 对业务语义、并发、兼容性和跨文件逻辑的审查。
- 风险项只是审查线索。工具不证明代码正确，也不代替测试、安全审查和发布审批。

退出码：`0` 表示审查或验收成功，`1` 表示回归门槛未通过，`2` 表示输入、配置或 LLM 调用错误。

## 验证

```bash
python -m unittest discover -s tests -v
```

