---
name: opencode-cpa-image-input
description: Use when OpenCode or another OpenAI-compatible client on CLIProxyAPI/CPA shows image-input failures, connection errors, invalid API keys, or stale Codex model metadata. 适用于 OpenCode 或其他 OpenAI 兼容客户端在 CLIProxyAPI/CPA 上出现图片输入失败、连接失败、API key 错误或 Codex 模型元数据过旧的场景。
---

# OpenCode CPA 图片输入排障 / Image Input Troubleshooting

当 OpenCode 连接自定义 OpenAI 兼容 provider，例如 CLIProxyAPI / CPA，但出现“模型不支持图片输入”“Unable to connect”“Invalid API key”或模型缓存过旧时，使用这个 skill。

Use this skill when OpenCode is pointed at a custom OpenAI-compatible provider such as CLIProxyAPI / CPA, but image uploads fail, the client cannot connect, or Codex model metadata is stale.

## 先检查 / First checks

1. 确认服务端可达，基础 URL 以 `/v1` 结尾。
1. Confirm the endpoint is reachable and the base URL ends in `/v1`.
2. 确认客户端已填写 API key，并且与服务端允许的 key 一致。
2. Confirm the client-side API key matches the server-side allowed key.
3. 确认 `/v1/models` 返回的 Codex 模型包含图片能力元数据。
3. Confirm `/v1/models` returns Codex models with image capability metadata.
4. 刷新或重新创建 OpenCode provider，让它不要继续用旧缓存。
4. Refresh or recreate the OpenCode provider so it stops using cached model metadata.

## 标准排障流程 / Known-good flow

当用户报告 `does not support image input`、`Unable to connect`，或者 `gpt-5.4` / `gpt-5.4-mini` 没有图片能力时：

When the user reports `does not support image input`, `Unable to connect`, or missing image support on `gpt-5.4` / `gpt-5.4-mini`:

1. 先用配置好的 API key 直接测试服务端。
1. Test the server directly with the configured API key.
2. 检查 `/v1/models` 是否包含 `supportedInputModalities` 或等价的 `modalities.input`。
2. Check whether `/v1/models` includes `supportedInputModalities` or equivalent `modalities.input` data.
3. 如果服务端元数据不对，先修服务端并重新部署。
3. If the server metadata is wrong, fix and redeploy the server first.
4. 如果服务端元数据正确，再刷新 OpenCode 模型缓存或重建 provider。
4. If the server metadata is correct, refresh OpenCode's model cache or recreate the provider.
5. 如果客户端仍然拦图片，再在自定义 provider 里显式声明图片输入能力。
5. If the client still blocks images, explicitly declare image input in the custom provider.

## 快速判断 / Fast decision tree

先判断是服务端问题还是客户端缓存问题。

First decide whether this is a server-side metadata problem or a client-side cache problem.

1. `curl` 直接打 `/v1/models`。
1. Test `/v1/models` directly with `curl`.
2. 如果返回里没有 `supportedInputModalities` 或图片能力，先修服务端。
2. If the response lacks `supportedInputModalities` or image support, fix the server first.
3. 如果返回里已经有图片能力，但 OpenCode 仍报错，刷新模型缓存或重建 provider。
3. If the server already advertises image support but OpenCode still fails, refresh the model cache or recreate the provider.
4. 如果 OpenCode 仍然把模型视为纯文本，显式补 `attachment: true` 和 `modalities.input`.
4. If OpenCode still treats the model as text-only, explicitly add `attachment: true` and `modalities.input`.

## 客户端配置规则 / Client-side config rules

在 OpenCode 里配置自定义 OpenAI 兼容 provider 时：

When configuring a custom OpenAI-compatible provider in OpenCode:

- 模型选择使用 `provider_id/model_id`。
- Use `provider_id/model_id` in the model selector.
- 基础 URL 要指向 OpenAI 兼容根地址，通常以 `/v1` 结尾。
- Keep the base URL at the OpenAI-compatible root, usually ending in `/v1`.
- 在 provider 配置里填准确的 API key。
- Provide the exact API key in the provider config.
- 如果模型支持图片但 OpenCode 没有自动识别，就显式设置 `attachment: true` 和 `modalities.input: ["text", "image"]`。
- If a model supports images but OpenCode does not infer that automatically, set `attachment: true` and `modalities.input: ["text", "image"]` explicitly.

## 常用验证命令 / Common verification commands

```bash
curl -sS http://HOST:18081/v1/models -H "Authorization: Bearer YOUR_KEY"
```

```bash
opencode models <provider_id> --refresh
```

```bash
opencode models <provider_id> --verbose
```

When debugging on the server side, confirm these two facts:

- The request reaches the same port you configured in OpenCode.
- The API key in OpenCode exactly matches one of the server's allowed keys.

## 恢复路径 / Recovery path

如果 provider 看起来没问题，但 OpenCode 还是说模型不能读图：

If the provider looks correct but OpenCode still says the model cannot read images:

1. 删除这个 provider。
1. Remove the provider.
2. 用正确的 base URL 和 API key 重新创建。
2. Recreate it with the correct base URL and API key.
3. 刷新模型缓存。
3. Refresh the model cache.
4. 如果能力仍然旧，重启 OpenCode。
4. Restart OpenCode if it still shows stale capabilities.

## 不要误判 / Common mistakes

- `http` / `https` 写错。
- `baseURL` 少了 `/v1`。
- API key 填到了错误的字段。
- 用了旧的 provider 名称，但 OpenCode 缓存里还保留着新 provider 的旧能力。
- 只改了服务端源码，没有重启真正运行的二进制。

## 参考 / Reference

见 [runbook.md](references/runbook.md)，里面有简明验证清单和最小 JSONC 示例。

See [runbook.md](references/runbook.md) for a compact verification checklist and a minimal JSONC example.
