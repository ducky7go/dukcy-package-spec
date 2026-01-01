# Duckov Package Spec

[《逃离鸭科夫》](https://github.com/xvrsl/duckov_modding) 游戏模组打包规范。

## 概述

本仓库定义了《逃离鸭科夫》游戏模组的标准化规范，包括模组文件夹结构、元数据格式、扩展功能和 NuGet 打包规范。

### 规范层级

```
┌─────────────────────────────────────────────────────────────┐
│  游戏原生模组核心规范 v1.0                                    │
│  - 定义必需文件：info.ini, [name].dll, preview.png           │
└─────────────────────────────────────────────────────────────┘
                              ↓
┌─────────────────────────────────────────────────────────────┐
│  游戏原生模组扩展规范 v1.0                                    │
│  - 定义扩展字段：author, license, homepage, dependencies 等  │
│  - 定义本地化：description/, title/, wiki/                   │
└─────────────────────────────────────────────────────────────┘
                              ↓
┌─────────────────────────────────────────────────────────────┐
│  NuGet 模组打包规范 v1.0                                      │
│  - 定义打包转换规则                                           │
│  - 定义 NuGet 特有结构（.nuspec, content/）                   │
└─────────────────────────────────────────────────────────────┘
```

## 规范文档

| 规范 | 版本 | 状态 | 描述 |
|------|------|------|------|
| [游戏原生模组规范](specs/game-native-mod/v1.0/spec.md) | 1.0 | 正式版 | 定义模组的核心文件夹结构和必需文件 |
| [游戏原生模组扩展规范](specs/game-native-mod-ext/v1.0/spec.md) | 1.0 | 正式版 | 定义扩展元数据和本地化支持 |
| [NuGet 模组打包规范](specs/nuget-mod-packaging/v1.0/spec.md) | 1.0 | 正式版 | 定义将模组打包为 NuGet 包的转换规则 |

## 快速开始

### 最小模组结构

一个有效的模组至少需要以下文件：

```
MyMod/
├── info.ini          # 模组元数据
├── MyMod.dll         # 模组程序集
└── preview.png       # 预览图标
```

### info.ini 示例

```ini
name = MyMod
displayName = 我的模组
description = 这是一个示例模组
version = 1.0.0
author = 作者名
license = MIT
```

### 本地化支持（可选）

```
MyMod/
├── description/
│   ├── zh.md      # 简体中文描述
│   └── en.md      # 英文描述
└── wiki/
    └── zh/
        └── Index.md
```

## 关键约束

- `info.ini` 中的 `name` 字段必须与 DLL 文件名（不含扩展名）匹配
- `dependencies` 字段仅支持模组名称，不支持版本范围
- 所有文本文件必须使用 UTF-8 编码
- 预览图标推荐：128x128 PNG，最大 50KB

## 相关链接

- [Escape from Duckov 官方模组文档](https://github.com/xvrsl/duckov_modding)
- [NuGet 包格式文档](https://learn.microsoft.com/en-us/nuget/reference/nuspec)

## 许可证

本规范文档可自由使用和修改。

---

**注意**：本仓库使用 [OpenSpec](openspec/AGENTS.md) 进行规范变更管理。
