# ccll fork of dsh-GOkifu-compact

本仓库是 `MYCF711/dsh-GOkifu-compact` v1.19.1 的 fork，供 sim 节点的 DSH 使用。

## 与上游的差异

1. **设置页 profile 回落**：`POST /gokifu/api` 的 `state` / `save` 在请求不带 `profile` 时使用当前宿主 profile（`ctx.get("profileContext").name`）；取不到时按 `DSH_PROFILE`，再回落 `desktop`；不再一律回落 `desktop`。
2. **`firstFoldRatio` 小数保留**：`validatePatch` 不再对该键 `Math.round`，`0.5` 不会写成 `1`。
3. **四个 preset 的默认配置**：`thresholdRatio: 0.85`、`firstFoldRatio: 0.5`、`headroomTokens: 65536`、`retainRatio: 0.16`、`growthFloorTokens: 40000`、`maxTokens: 8192`、`summaryMode: hybrid`、`hotLedger: true`、`hotLedgerMaxTokens: 500`。

## 版本与安装

- 版本：`1.19.1-ccll.1`
- 安装（sim，profile `web`，PowerShell）：

  ```powershell
  Set-Location "$env:USERPROFILE\.dsh\profiles\web"
  pnpm add "github:ccll/dsh-gokifu-compact#v1.19.1-ccll.1"
  ```

  不要用 `dsh plugin --profile web add ...`：当前 DSH 版本会持有 `package.json.lock`，而 pnpm 又要写同一个 `package.json`，命令会在 pnpm 实际完成后仍挂住不退出（2026-11-20 sim 实测）。直接 `pnpm add` 可绕过这个死锁。

  然后重启 DSH。
- 验证：`POST /gokifu/api {"action":"state"}` 应返回 `profile: "web"`、`version: "1.19.1-ccll.1"`、`thresholdRatio: 0.85`、`firstFoldRatio: 0.5`；设置页也应显示 85% / 0.5。

## 上游同步

```bash
git fetch upstream --tags
git switch ccll-1.19.1
git rebase v1.19.1   # 或 merge
```

本 fork 的 patch 落在 `src/ui.mjs`、`src/rooms/config-schema.mjs`、`cordis.patch.yml`；升级上游后重新检查这三处是否仍需要。

## 许可

MIT，见 `LICENSE`；上游版权归 MYCF711。
