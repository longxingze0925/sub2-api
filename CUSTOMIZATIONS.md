# 本仓库定制

本仓库以 `UPSTREAM_BASE` 记录当前合入的官方 Sub2API Release。

## 保留的定制

1. API Key 账号的 Codex 模型目录在本地生成。目录使用账号公开模型映射；没有显式映射时使用本地 OpenAI 默认目录，不请求上游 `/models`。
2. Codex 模型目录的上游错误保留 HTTP 状态码，但客户端不接收上游 URL、IP、路径、原始响应正文或传输错误细节。

`SetAPIKeyCodexModelsUpstreamCompatibilityForTesting` 只用于测试官方 API Key 上游目录兼容路径，生产代码默认关闭。

## 补丁文件

首版将两项定制按提交顺序固化在 `patches/`：

```bash
git am patches/0001-feat-codex-use-local-API-key-model-catalog.patch
git am patches/0002-fix-codex-sanitize-upstream-model-errors.patch
```

补丁基线是 `UPSTREAM_BASE` 所记录的官方标签。正常使用本仓库时无需手工应用；它们保留为审计和冲突处理材料。

## 同步官方版本

`.github/workflows/sync-upstream.yml` 每日检查官方最新稳定 Release。发现新版本时，它会从本仓库主分支创建同步分支、合并官方标签、更新 `UPSTREAM_BASE`、运行模型目录相关测试与后端构建，然后创建 Pull Request。

两项定制以独立提交保留在主线中，因此正常同步会自动携带；发生冲突时工作流失败，不创建 PR，人工解决后重新运行即可。

通过同步 PR 后，推送形如 `v0.2.1-custom.1` 的标签即可触发现有 Release 工作流，发布 `ghcr.io/longxingze0925/sub2-api:v0.2.1-custom.1` 镜像。
