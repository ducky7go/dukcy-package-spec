## OpenSpec

This repo has no `openspec/` directory. OpenSpec for all `ducky7go-mono` sub-repos lives in the parent repo: `../../openspec/` (workflow rules `../../openspec/AGENTS.md`, specs `../../openspec/specs/`, changes `../../openspec/changes/`). This repo's former project context is `../../openspec/projects/dukcy-package-spec/project.md`.


# dukcy-package-spec

## 项目概述

《逃离鸭科夫》（Escape from Duckov）游戏模组的打包规范仓库，全部是 Markdown 文档，没有代码、构建或测试。规范分三层，后一层建立在前一层之上：游戏原生模组核心规范 -> 游戏原生模组扩展规范 -> NuGet 模组打包规范。`ducky7go/ducky-cli` 与 `ducky7go/action-ducky-nuget` 按本规范实现打包/发布，因此这里的改动会影响它们。文档语言为中文（README 与规范正文），`../../openspec/projects/dukcy-package-spec/project.md` 为英文。

## 目录结构

- `specs/game-native-mod/v1.0/spec.md` - 核心规范：最小模组结构（`info.ini`、`[name].dll`、`preview.png`）、必需字段 `name`/`displayName`/`description`、可选字段 `version`/`tags`/`publishedFileId`、DLL 命名、预览图。
- `specs/game-native-mod-ext/v1.0/spec.md` - 扩展字段（如 `author`/`authors`、`license`、`homepage`、`dependencies`、`copyright`）、本地化（`description/`、`title/`，BCP 47）、`wiki/`。
- `specs/nuget-mod-packaging/v1.0/spec.md` - `info.ini` 到 `.nuspec` 的映射、通配符打包到 `content/`、`preview.png` 到 `icon.png`、`.nuget/marker.txt` 标记文件、依赖、合规要求、完整示例。
- `README.md` - 规范层级图、规范索引表、关键约束。

## 构建 / 测试 / 运行

无。仓库中没有 `package.json`、项目文件或 CI 工作流（`../../openspec/projects/dukcy-package-spec/project.md` 也写明测试策略为"不适用"）。修改后人工检查各规范之间的引用、章节编号与示例是否一致。

## 约定

- 每份规范文件头部带 `版本`、`状态`、`日期`，末尾有 `变更历史` 表；改动规范时同步更新变更历史（当前所有规范均为 1.0，2025-12-31，初始规范）。
- 章节编号、字段表格格式在三份规范间保持一致；示例使用 `info.ini` 的 `key = value` 平铺格式。
- 版本目录是 `v1.0/`；破坏性变更应新增版本目录而不是原地覆盖（目录结构按此设计，仓库中尚无第二个版本可参考）。
- 规范变更走 OpenSpec 提案流程（见上方 OpenSpec 说明与 `../../openspec/AGENTS.md`）。

## 关键约束（来自规范，修改时必须保持）

- `info.ini` 的 `name` 必须与 DLL 文件名（不含扩展名）一致，且仅含字母数字、点、连字符、下划线。
- `dependencies` 字段只写模组名称，不含版本范围；版本范围只能写在 `.nuspec` 里。
- 文本文件必须为 UTF-8；预览图推荐 128x128 PNG、不超过 50KB。
- 映射优先级：`author` 优先于 `authors`；`projectUrl` 优先于 `homepage`。

## 注意事项

- `../../openspec/projects/dukcy-package-spec/project.md` 把游戏名写作《逃离达科夫》，README 与规范正文写作《逃离鸭科夫》，两者不一致；以 README/规范正文为准。
- `../../openspec/projects/dukcy-package-spec/project.md` 引用 `Newbe.BetterModLoader`（https://github.com/Newbe36524/Newbe.BetterModLoader）作为外部参考，规范中的示例模组为 `DisplayItemValue`。

## 相关仓库

- `ducky7go/ducky-cli` - 规范的 CLI 实现（`ducky nuget pack/push/validate`）。
- `ducky7go/action-ducky-nuget` - 基于 ducky-cli 的 GitHub Action。
- 游戏官方模组文档：https://github.com/xvrsl/duckov_modding
