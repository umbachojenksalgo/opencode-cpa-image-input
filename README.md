# OpenCode CPA Image Input Skill

English | 中文

This repository contains a Codex/Claude-compatible skill for troubleshooting OpenCode image-input failures on custom OpenAI-compatible providers, especially CLIProxyAPI / CPA.

本仓库提供一个用于排查 OpenCode 在自定义 OpenAI 兼容 provider 上图片输入失败问题的 skill，主要面向 CLIProxyAPI / CPA 场景。

## What this skill helps with / 适用场景

- OpenCode says `does not support image input`
- OpenCode says `Unable to connect`
- OpenCode says `Missing API key` or `Invalid API key`
- `/v1/models` is reachable, but the client still treats Codex models as text-only
- OpenCode model metadata is stale or cached incorrectly

## What it checks / 它会检查什么

1. Whether the server responds at the configured `/v1` endpoint.
2. Whether the API key matches the server-side allowlist.
3. Whether `/v1/models` includes image capability metadata.
4. Whether OpenCode needs a provider refresh or cache reset.

1. 服务端是否能正常响应配置好的 `/v1` 接口。
2. API key 是否与服务端允许的 key 一致。
3. `/v1/models` 是否包含图片能力元数据。
4. OpenCode 是否需要刷新 provider 或模型缓存。

## Included files / 文件说明

- [`SKILL.md`](SKILL.md) - main bilingual skill instructions
- [`references/runbook.md`](references/runbook.md) - compact troubleshooting runbook
- [`agents/openai.yaml`](agents/openai.yaml) - UI metadata for Codex skill lists

## Quick use / 快速使用

Use the skill when you need to diagnose image upload failures against a custom OpenAI-compatible provider.

当你需要排查自定义 OpenAI 兼容 provider 的图片上传失败时，直接使用这个 skill。

## Repository goal / 仓库目标

This repository is intentionally small and practical: it documents a single troubleshooting path, with the goal of making OpenCode + CPA image support easy to restore and verify.

这个仓库刻意保持小而实用：只记录一条排障路径，目标是让 OpenCode + CPA 的图片支持更容易恢复和验证。
