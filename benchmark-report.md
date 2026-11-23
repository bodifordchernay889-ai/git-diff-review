# Diff Review 回归验收

- 数据集：20 条去敏历史缺陷模式回归集
- 后端：`rules`
- 结论：**通过**
- 问题样本命中：10/10（100.0%）
- 正常样本误报：0/10（门槛 ≤ 2）
- 执行完成：是

| 案例 | 标签 | 结果 | 说明 |
| --- | --- | --- | --- |
| `01_sql_injection` | 含问题 | 通过 | 命中 `sql_injection` |
| `02_hardcoded_secret` | 含问题 | 通过 | 命中 `hardcoded_secret` |
| `03_command_injection` | 含问题 | 通过 | 命中 `command_injection` |
| `04_eval_exec` | 含问题 | 通过 | 命中 `eval_exec` |
| `05_mutable_default` | 含问题 | 通过 | 命中 `mutable_default` |
| `06_unsafe_deserialization` | 含问题 | 通过 | 命中 `unsafe_deserialization` |
| `07_tls_disabled` | 含问题 | 通过 | 命中 `tls_disabled` |
| `08_authorization_removed` | 含问题 | 通过 | 命中 `authorization_removed` |
| `09_sensitive_logging` | 含问题 | 通过 | 命中 `sensitive_logging` |
| `10_path_traversal` | 含问题 | 通过 | 命中 `path_traversal` |
| `11_parameterized_sql` | 正常 | 通过 | 无误报 |
| `12_env_secret` | 正常 | 通过 | 无误报 |
| `13_safe_subprocess` | 正常 | 通过 | 无误报 |
| `14_json_deserialization` | 正常 | 通过 | 无误报 |
| `15_tls_enabled` | 正常 | 通过 | 无误报 |
| `16_specific_exception` | 正常 | 通过 | 无误报 |
| `17_context_manager` | 正常 | 通过 | 无误报 |
| `18_authorization_added` | 正常 | 通过 | 无误报 |
| `19_redacted_logging` | 正常 | 通过 | 无误报 |
| `20_docs_only` | 正常 | 通过 | 无误报 |

## 验收门槛

- [x] 含问题 diff 命中率 ≥ 70%
- [x] 正常 diff 误报 ≤ 2 条
- [x] 全部案例执行完成
- [x] 数据分布为 10 含问题 / 10 正常
