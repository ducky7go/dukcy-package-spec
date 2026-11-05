# dukcy-package-spec

## 项目概述

《逃离鸭科夫》（Escape from Duckov）游戏模组的打包规范仓库，全部是 Markdown 文档，没有代码、构建或测试。规范分三层，后一层建立在前一层之上：游戏原生模组核心规范 -> 游戏原生模组扩展规范 -> NuGet 模组打包规范。`ducky7go/ducky-cli` 与 `ducky7go/action-ducky-nuget` 按本规范实现打包和发布，这里的改动会影响它们。文档语言为中文（README 与规范正文）。

## 目录结构

- `specs/game-native-mod/v1.0/spec.md` - 核心规范：必需文件 `info.ini`、`[name].dll`、`preview.png`；`info.ini` 必需字段 `name`/`displayName`/`description`；可选字段 `version`/`tags`/`publishedFileId`；DLL 命名与预览图。
- `specs/game-native-mod-ext/v1.0/spec.md` - 扩展字段（`author`/`authors`、`license`、`homepage`/`projectUrl`、`dependencies`、`copyright` 等）、本地化（`description/`、`title/`、`wiki/`，使用 BCP 47 语言代码）。
- `specs/nuget-mod-packaging/v1.0/spec.md` - `info.ini` 到 `.nuspec` 的映射、通配符打包到 `content/`、`preview.png` 复制为 `icon.png`、`marker.txt` 标记文件、依赖、示例。
- `README.md` - 规范层级图、规范索引表、关键约束。

## 构建 / 测试 / 运行

无。仓库中没有 `package.json`、项目文件或 CI 工作流。修改后人工检查各规范之间的引用、章节编号与示例是否一致。

## 约定

- 每份规范文件头部带 `版本`、`状态`、`日期`，末尾有 `变更历史` 表；改动规范时同步更新变更历史（当前三份规范均为 1.0，日期 2025-12-31）。
- 章节编号、字段表格格式在三份规范间保持一致；示例使用 `info.ini` 的 `key = value` 平铺格式。
- 版本目录是 `v1.0/`；破坏性变更应新增版本目录而不是原地覆盖（仓库中尚无第二个版本可参考）。
- 规范变更遵循 OpenSpec 工作流程。
- 字段、文件名或打包规则变化时，要同步检查 `ducky-cli` 与 `action-ducky-nuget` 的实现是否仍然符合。

## 关键约束（来自规范，修改时必须保持）

- `info.ini` 的 `name` 必须与 DLL 文件名（不含扩展名）一致，且仅含字母数字、点、连字符、下划线。
- `dependencies` 字段只写模组名称，不含版本范围；版本范围只能写在 `.nuspec` 里。
- 文本文件必须为 UTF-8；预览图推荐 128x128 PNG、不超过 50KB。
- 映射优先级：`author` 优先于 `authors`；`projectUrl` 优先于 `homepage`。

## 相关仓库

- `ducky7go/ducky-cli` - 规范的 CLI 实现（`ducky nuget pack/push/validate`）。
- `ducky7go/action-ducky-nuget` - 基于 ducky-cli 的 GitHub Action。
- 游戏官方模组文档：https://github.com/xvrsl/duckov_modding
