# 游戏原生模组规范 v1.0

**版本：** 1.0
**状态：** 正式版
**日期：** 2025-12-31

---

## 摘要

本规范定义了《逃离达科夫》游戏模组的原生文件夹结构、元数据格式和文件组织方式。它作为模组开发的基础定义，独立于任何打包或分发机制。

本规范定义了模组的**核心要求**。扩展功能（如本地化支持）见[游戏原生模组扩展规范 v1.0](../game-native-mod-ext/v1.0/spec.md)。

---

## 1. 简介

### 1.1 目的

游戏原生模组规范建立了以下约定：
- **模组文件夹结构** - 必需和可选文件
- **元数据格式** - `info.ini` 架构和验证
- **文件命名** - DLL 命名约定

### 1.2 范围

**包含范围：**
- 原生模组文件夹结构
- `info.ini` 元数据格式
- DLL 命名约定
- 预览图标规范

**不包含范围：**
- 本地化支持（见[游戏原生模组扩展规范 v1.0](../game-native-mod-ext/v1.0/spec.md)）
- 打包/分发格式（NuGet、ZIP 等）
- 模组加载运行时行为
- 游戏特定的模组 API 表面
- 模组加载器实现细节

### 1.3 参考实现

本规范基于 `DisplayItemValue`（一个用于《逃离达科夫》的最小模组），展示了基本的模组结构：
- 基于 INI 的元数据（`info.ini`）
- 单个 DLL 程序集（`DisplayItemValue.dll`）
- 预览图标（`preview.png`）

---

## 2. 核心要求

### 2.1 最小可行模组结构

一个有效的模组必须至少包含：

```
MyMod/
├── info.ini          # 必需：模组元数据
├── MyMod.dll         # 必需：主模组程序集
└── preview.png       # 必需：模组预览图标
```

**必需文件：**
| 文件 | 描述 |
|------|------|
| `info.ini` | INI 格式的模组元数据 |
| `[name].dll` | 主模组程序集（文件名必须与 `info.ini` 中的 `name` 字段匹配） |
| `preview.png` | 模组预览图标（推荐：128x128 PNG，最大 50KB） |

### 2.2 文件夹命名约定

- 模组文件夹名称应与 `info.ini` 中的 `name` 字段匹配
- 出于组织目的，文件夹可以不同
- 模组加载器必须将 `info.ini` 中的 `name` 字段作为权威标识符

### 2.3 info.ini 元数据格式

#### 2.3.1 必需字段

| 字段 | 类型 | 描述 | 示例 |
|------|------|------|------|
| `name` | 字符串 | 唯一模组标识符 | `DisplayItemValue` |
| `displayName` | 字符串 | 人类可读名称 | `显示物品价值` |
| `description` | 字符串 | 简短描述 | `能够显示物品的价值！！！！by xvrsl` |

**约束条件：**
- `name` 必须仅包含字母数字字符、点、连字符或下划线
- `name` 必须与 DLL 文件名匹配（不含扩展名）
- 文件编码必须为 UTF-8

#### 2.3.2 可选字段

| 字段 | 类型 | 描述 | 示例 |
|------|------|------|------|
| `version` | 字符串 | 语义版本（SemVer 2.0） | `1.0.0` |
| `tags` | 字符串 | 逗号分隔的搜索标签 | `ui, items, utility` |
| `publishedFileId` | 字符串 | Steam 创意工坊已发布文件 ID | `3532400883` |

#### 2.3.3 INI 格式约束

- 无 `[节]` 的扁平结构
- 每个字段指定为 `key = value`
- 键和值周围的空格将被忽略

**示例（DisplayItemValue 模组）：**
```ini
name = DisplayItemValue
displayName = 显示物品价值
description = 能够显示物品的价值！！！！by xvrsl

publishedFileId = 3532400883
```

### 2.4 DLL 命名约定

**关键：** `info.ini` 中的 `name` 字段必须与 DLL 文件名（不含 `.dll` 扩展名）匹配。

| `info.ini` name | DLL 文件名 | 有效 |
|-----------------|------------|------|
| `DisplayItemValue` | `DisplayItemValue.dll` | 是 |
| `My.Mod` | `My.Mod.dll` | 是 |
| `MyMod` | `DifferentName.dll` | **否** - 不匹配 |
| `MyMod` | `MyMod.exe` | **否** - 扩展名错误 |

**验证规则：**
- DLL 文件必须存在于模组文件夹根目录
- DLL 文件名必须精确为 `[name].dll`
- 支持带点的名称（如 `My.Company.Mod`）

### 2.5 预览图标

模组必须包含预览图标，用于在模组列表和市场中显示。

**要求：**
- 文件必须命名为 `preview.png`
- 文件必须为 PNG 格式
- 推荐尺寸：128x128 像素
- 最大大小：50KB
- 任何正方形分辨率均可接受（64x64、256x256 等）

---

## 3. 示例

### 3.1 最小可行模组

```
MyMod/
├── info.ini
├── MyMod.dll
└── preview.png
```

**info.ini:**
```ini
name = MyMod
displayName = My First Mod
description = A simple example mod
```

### 3.2 完整模组示例

包含可选字段的模组：

**info.ini:**
```ini
name = MyMod
displayName = My First Mod
description = A simple example mod
version = 1.0.0
tags = ui, utility
publishedFileId = 1234567890
```

---

## 4. 验证

### 4.1 最小验证清单

模组必须满足以下条件才能被视为有效：

- `info.ini` 存在并包含所有必需字段
- `[name].dll` 存在且与 `name` 字段匹配
- `preview.png` 存在且为有效的 PNG 文件
- 所有指定字段具有有效的格式和值
- 文本文件的文件编码为 UTF-8（包括 `info.ini`）

---

## 5. 相关规范

- [游戏原生模组扩展规范 v1.0](../game-native-mod-ext/v1.0/spec.md) - 定义本地化等扩展功能
- [NuGet 模组打包规范 v1.0](../nuget-mod-packaging/v1.0/00-specification.md) - 定义如何将原生模组打包为 NuGet 包

---

## 6. 参考文献

- [Escape from Duckov 官方模组文档](https://github.com/xvrsl/duckov_modding/raw/refs/heads/main/README.md)
- [语义版本控制 2.0.0](https://semver.org/)
- [SPDX 许可证列表](https://spdx.org/licenses/)

---

## 变更历史

| 版本 | 日期 | 变更 |
|------|------|------|
| 1.0 | 2025-12-31 | 初始规范 |
