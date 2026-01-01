# 游戏原生模组扩展规范 v1.0

**版本：** 1.0
**状态：** 正式版
**日期：** 2025-12-31

---

## 摘要

本规范定义了《逃离达科夫》游戏模组的可选扩展功能。这些扩展建立在[游戏原生模组核心规范](../game-native-mod/v1.0/spec.md)的基础上，为模组提供增强功能。

本规范包含：
- **扩展元数据字段** - 作者、许可证、主页等额外信息
- **本地化支持** - 多语言内容组织
- **Wiki 文档支持** - 模组文档和使用说明
- **验证规范** - 扩展内容的验证标准

---

## 1. 简介

### 1.1 目的

游戏原生模组扩展规范建立了以下约定：
- **扩展元数据** - `info.ini` 中的可选扩展字段
- **本地化文件格式** - 多语言内容组织方式
- **Wiki 文档格式** - 模组文档的组织方式
- **文件命名** - BCP 47 语言代码的使用
- **文件夹结构** - 扩展资源的组织方式

### 1.2 范围

**包含范围：**
- `info.ini` 扩展元数据字段
- `description/` 文件夹格式
- `title/` 文件夹格式
- `wiki/` 文件夹格式

**不包含范围：**
- 核心模组结构（见[游戏原生模组核心规范](../game-native-mod/v1.0/spec.md)）
- 打包/分发格式
- 模组加载运行时行为

---

## 2. 扩展元数据字段

模组可在 `info.ini` 中包含额外的元数据字段，用于提供更完整的模组信息。

### 2.1 可选扩展字段

| 字段 | 类型 | 描述 | 示例 |
|------|------|------|------|
| `author` | 字符串 | 单个作者名称 | `Developer Name` |
| `authors` | 字符串 | 逗号分隔的作者名称（多个作者时使用） | `作者一, 作者二` |
| `license` | 字符串 | SPDX 许可证标识符 | `MIT` |
| `homepage` | 字符串 | 项目主页 URL | `https://example.com` |
| `projectUrl` | 字符串 | 项目主页 URL（与 `homepage` 互为别名） | `https://example.com` |
| `copyright` | 字符串 | 版权信息 | `Copyright 2025` |
| `readme` | 字符串 | README 文件路径或内容 | `README.md` |
| `releaseNotes` | 字符串 | 发布说明文件路径或内容 | `CHANGELOG.md` |
| `dependencies` | 字符串 | 逗号分隔的依赖模组名称 | `OtherMod, AnotherMod` |

**说明：**
- `author` 用于单个作者，`authors` 用于多个作者（逗号分隔）
- `homepage` 和 `projectUrl` 互为别名，可互换使用
- `readme` 和 `releaseNotes` 可以是文件路径（如 `README.md`）或直接内容

**示例：**
```ini
; 核心必需字段（见核心规范）
name = MyMod
displayName = My First Mod
description = A simple example mod

; 扩展元数据字段
author = Developer Name
authors = 作者一, 作者二
license = MIT
homepage = https://example.com
projectUrl = https://example.com
copyright = Copyright 2025
readme = README.md
releaseNotes = CHANGELOG.md
dependencies = OtherMod, AnotherMod
```

---

## 3. 本地化支持

模组可通过多种机制提供本地化内容。所有本地化文件均使用 BCP 47 语言代码。

### 3.1 常用 BCP 47 语言代码

| 代码 | 语言 |
|------|------|
| `en` | 英语 |
| `zh` | 中文（简体） |
| `zh-hant` | 中文（繁体） |
| `de` | 德语 |
| `es` | 西班牙语 |
| `fr` | 法语 |
| `ja` | 日语 |
| `ko` | 韩语 |

### 3.2 多语言回退规则

当模组加载器或平台需要显示本地化内容时，应按以下优先级查找匹配的语言文件：

1. **首选语言** - 用户当前设置的语言（如 `zh`）
2. **简体中文（zh）** - 如果首选语言不可用
3. **英语（en）** - 如果简体中文不可用
4. **第一个可用文件** - 如果以上都不可用，使用找到的第一个文件

**示例：** 用户设置为德语（`de`），查找顺序：`de.md` → `zh.md` → `en.md` → 第一个可用文件

### 3.3 Description 文件夹

用于在 Steam 创意工坊或其他平台展示的多语言模组描述：

```
MyMod/
└── description/
    ├── zh.md      # 简体中文描述（推荐）
    ├── en.md      # 英文描述
    └── de.md      # 德文描述
```

**格式：** Markdown 文件
**命名：** `[语言代码].md`
**用途：** 在 Steam 创意工坊、模组市场等平台展示模组的完整描述
**建议：** 至少提供 `zh.md`，推荐同时提供 `en.md`

### 3.4 Title 文件夹

用于模组的多语言标题显示：

```
MyMod/
└── title/
    ├── zh.md      # 简体中文标题
    ├── en.md      # 英文标题
    └── de.md      # 德文标题
```

**格式：** Markdown 文件
**命名：** `[语言代码].md`
**用途：** 在不同语言环境下显示模组的本地化名称

---

## 4. Wiki 文档支持

模组可包含 `wiki/` 文件夹，用于存放模组的详细文档和使用说明。

### 4.1 文件夹结构

Wiki 使用按语言分组的文件夹结构，支持与本地化相同的回退规则：

```
MyMod/
└── wiki/
    ├── zh/                    # 简体中文文档（推荐）
    │   ├── Index.md           # 必需：文档入口页
    │   ├── Installation.md
    │   ├── Usage.md
    │   └── guides/
    │       └── GettingStarted.md
    └── en/                    # 英文文档
        ├── Index.md           # 必需：文档入口页
        ├── Installation.md
        ├── Usage.md
        └── guides/
            └── GettingStarted.md
```

**结构：** `wiki/[语言代码]/*.md`
**说明：** Wiki 必须使用按语言分组的文件夹结构，不支持其他组织方式

### 4.2 要求

- 每个语言文件夹中 **Index.md** 必须存在，作为该语言的文档入口页面
- 所有文件必须为 Markdown 格式（`.md`）
- 文件名推荐使用 PascalCase 或 kebab-case
- 支持多级子文件夹组织
- 语言代码使用 BCP 47 标准（如 `zh`、`en`、`de`）

### 4.3 内容约定

- **Index.md** 应包含模组的概述、主要功能介绍和其他文档的导航链接
- 其他文档文件可包含安装说明、使用指南、配置说明、FAQ 等
- 支持标准的 Markdown 语法，包括标题、列表、代码块、链接等
- 各语言的文档内容应保持同步

---

## 5. 示例

### 5.1 扩展元数据字段示例

在 `info.ini` 中添加扩展字段：

```ini
; 核心必需字段（见核心规范）
name = MyMod
displayName = My First Mod
description = A simple example mod

; 扩展元数据字段
author = Developer Name
authors = 作者一, 作者二
license = MIT
homepage = https://example.com
projectUrl = https://example.com
copyright = Copyright 2025
readme = README.md
releaseNotes = CHANGELOG.md
dependencies = OtherMod, AnotherMod
```

### 5.2 本地化文件夹结构示例

```
MyMod/
├── description/
│   ├── zh.md      # 简体中文描述（推荐）
│   └── en.md      # 英文描述
└── title/
    ├── zh.md      # 简体中文标题
    └── en.md      # 英文标题
```

**说明：** 以上文件夹结构建立在核心规范定义的基础结构之上。多语言回退规则：首选语言 → zh → en → 第一个可用文件。

### 5.3 Wiki 文件夹结构示例

```
MyMod/
└── wiki/
    ├── zh/                    # 简体中文文档（推荐）
    │   ├── Index.md
    │   ├── Installation.md
    │   └── guides/
    │       ├── GettingStarted.md
    │       └── AdvancedUsage.md
    └── en/                    # 英文文档
        ├── Index.md
        ├── Installation.md
        └── guides/
            ├── GettingStarted.md
            └── AdvancedUsage.md
```

**说明：**
- Wiki 必须使用按语言分组的文件夹结构
- 每个语言文件夹中必须包含 Index.md
- 多语言回退规则：首选语言 → zh → en → 第一个可用文件

---

## 6. 验证

### 6.1 扩展内容验证

当存在可选文件夹时，内容应格式正确：

- 本地化文件使用有效的 BCP 47 代码
- Markdown 文件为有效的 Markdown 格式
- `wiki/` 文件夹存在时，每个语言子文件夹中必须包含 `Index.md` 文件
- Wiki 多语言文件夹应遵循结构：`wiki/[语言代码]/*.md`
- 当缺少首选语言时，应能正确回退到 zh → en → 第一个可用文件

---

## 7. 相关规范

- [游戏原生模组核心规范 v1.0](../game-native-mod/v1.0/spec.md) - 定义模组的必需核心结构
- [NuGet 模组打包规范 v1.0](../nuget-mod-packaging/v1.0/00-specification.md) - 定义如何将原生模组打包为 NuGet 包

---

## 8. 参考文献

- [BCP 47 语言标签](https://tools.ietf.org/html/bcp47)
- [SPDX 许可证列表](https://spdx.org/licenses/)
- [Escape from Duckov 官方模组文档](https://github.com/xvrsl/duckov_modding/raw/refs/heads/main/README.md)
- [Newbe.BetterModLoader](https://github.com/Newbe36524/Newbe.BetterModLoader)

---

## 变更历史

| 版本 | 日期 | 变更 |
|------|------|------|
| 1.0 | 2025-12-31 | 初始规范 |
