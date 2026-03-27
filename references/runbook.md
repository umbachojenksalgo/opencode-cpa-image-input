# OpenCode CPA 图片输入排障手册

## 覆盖的症状

- OpenCode 提示 `does not support image input`
- OpenCode 提示 `Unable to connect`
- 自定义 OpenAI 兼容 provider 返回 `Missing API key` 或 `Invalid API key`
- `/v1/models` 正常，但 OpenCode 仍把 Codex 模型当成纯文本模型

## 验证服务端

1. 确认接口返回 `200`：

```bash
curl http://HOST:18081/v1/models -H "Authorization: Bearer test-api-key-123"
```

2. 检查某个模型条目是否具备图片能力：

- `supportedInputModalities` 包含 `TEXT` 和 `IMAGE`
- 或客户端配置里已经写了 `modalities.input: ["text", "image"]`

## 验证 OpenCode 配置

- `provider_id/model_id` 要和 `opencode.jsonc` 里的 provider 与模型 key 对上
- `baseURL` 要以 `/v1` 结尾
- `apiKey` 要写在 provider 上
- 如果模型支持图片，建议写：

```jsonc
"attachment": true,
"modalities": {
  "input": ["text", "image"],
  "output": ["text"]
}
```

## 刷新模型缓存

使用 OpenCode CLI：

```bash
opencode models <provider_id> --refresh
```

如果 UI 里还是旧能力：

1. 删除 provider
2. 重新创建
3. 重启 OpenCode

## 最小可用示例

```jsonc
{
  "model": "cpa/gpt-5.4-mini",
  "provider": {
    "cpa": {
      "npm": "@ai-sdk/openai-compatible",
      "options": {
        "baseURL": "http://HOST:18081/v1",
        "apiKey": "test-api-key-123"
      },
      "models": {
        "gpt-5.4": {
          "name": "gpt-5.4",
          "attachment": true,
          "modalities": { "input": ["text", "image"], "output": ["text"] }
        },
        "gpt-5.4-mini": {
          "name": "gpt-5.4-mini",
          "attachment": true,
          "modalities": { "input": ["text", "image"], "output": ["text"] }
        }
      }
    }
  }
}
```
