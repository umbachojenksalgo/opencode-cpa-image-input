---
name: opencode-cpa-image-input
description: 用于排查 OpenCode 或其他 OpenAI 兼容客户端在自定义 CPA/CLIProxyAPI 供应商上出现的图片输入失败、无法连接、API key 错误或 Codex 模型元数据过旧问题。
---

# OpenCode CPA 图片输入排障

当 OpenCode 连接的是自定义 OpenAI 兼容供应商，例如 CLIProxyAPI / CPA，但出现“模型不支持图片输入”“Unable to connect”“Invalid API key”或模型列表缓存过旧时，使用这个 skill。

## 先检查这四项

1. 确认服务端可达，基础 URL 以 `/v1` 结尾。
2. 确认客户端已填写 API key，并且与服务端允许的 key 一致。
3. 确认 `/v1/models` 返回的 Codex 模型包含图片能力元数据。
4. 刷新或重新创建 OpenCode provider，让它不要继续用旧缓存。

## 标准排障流程

当用户报告 `does not support image input`、`Unable to connect`，或者 `gpt-5.4` / `gpt-5.4-mini` 没有图片能力时：

1. 先用配置好的 API key 直接测试服务端。
2. 检查 `/v1/models` 是否包含 `supportedInputModalities` 或等价的 `modalities.input`。
3. 如果服务端元数据不对，先修服务端并重新部署。
4. 如果服务端元数据正确，再刷新 OpenCode 模型缓存或重建 provider。
5. 如果客户端仍然拦图片，再在自定义 provider 里显式声明图片输入能力。

## 客户端配置规则

在 OpenCode 里配置自定义 OpenAI 兼容 provider 时：

- 模型选择使用 `provider_id/model_id`。
- 基础 URL 要指向 OpenAI 兼容根地址，通常以 `/v1` 结尾。
- 在 provider 配置里填准确的 API key。
- 如果模型支持图片但 OpenCode 没有自动识别，就显式设置 `attachment: true` 和 `modalities.input: ["text", "image"]`。

## 恢复路径

如果 provider 看起来没问题，但 OpenCode 还是说模型不能读图：

1. 删除这个 provider。
2. 用正确的 base URL 和 API key 重新创建。
3. 刷新模型缓存。
4. 如果能力仍然旧，重启 OpenCode。

## 参考

见 [runbook.md](references/runbook.md)，里面有简明验证清单和最小 JSONC 示例。
