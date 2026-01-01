# NuGet 模组打包规范 v1.0

**版本：** 1.0
**状态：** 正式版
**日期：** 2025-12-31

---

## 摘要

本规范定义了将游戏模组文件夹映射到 NuGet 包格式的标准。它使得模组分发可以复用 NuGet 的生态系统，包括搜索、版本管理和依赖解析。

**关系说明：** 本规范是基于[游戏原生模组扩展规范 v1.0](../../game-native-mod-ext/v1.0/spec.md)的打包适配层：
- **输入**：符合游戏原生模组规范的模组文件夹
- **输出**：NuGet 包（`.nupkg`）
- **核心作用**：定义打包前后的**结构差异**和**元数据映射**

---

## 1. 简介

### 1.1 目的

NuGet 模组打包规范建立从模组文件夹到 NuGet 包的转换约定：
- **打包转换**：模组文件夹 → NuGet 包
- **安装转换**：NuGet 包 → 模组文件夹
- **元数据映射**：`info.ini` → `.nuspec`
- **依赖管理**：使用 NuGet 的依赖解析机制

### 1.2 与父级规范的关系

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
│  NuGet 模组打包规范 v1.0（本规范）                             │
│  - 定义打包转换规则                                           │
│  - 定义 NuGet 特有结构（.nuspec, content/）                   │
└─────────────────────────────────────────────────────────────┘
```

**本规范假设读者已熟悉父级规范的内容，不再重复说明。**

---

## 2. 打包转换规则

### 2.1 结构转换

**打包前（模组文件夹）：**
```
MyMod/
├── info.ini              # 模组元数据
├── MyMod.dll             # 模组程序集
├── preview.png           # 预览图标
├── description/          # 本地化描述（可选）
│   ├── zh.md
│   └── en.md
└── wiki/                 # 文档（可选）
    └── zh/
        └── Index.md
```

**打包后（NuGet 包）：**
```
MyMod.1.0.0.nupkg
├── MyMod.nuspec          # NuGet 包清单（自动生成）
├── icon.png              # 包图标（从 preview.png 复制）
└── content/              # 模组文件（原样保留）
    ├── info.ini
    ├── MyMod.dll
    ├── preview.png
    ├── description/
    │   ├── zh.md
    │   └── en.md
    └── wiki/
        └── zh/
            └── Index.md
```

**关键差异：**
1. 所有模组文件放入 `content/` 目录
2. `preview.png` 复制到根目录作为 `icon.png`
3. `info.ini` 的元数据映射到 `.nuspec` 文件

### 2.2 元数据映射

| info.ini 字段 | .nuspec 元素 | 转换规则 |
|---------------|-------------|---------|
| `name` | `<id>` | 直接映射，必须是有效的 NuGet ID |
| `version` | `<version>` | 直接映射，必须是 SemVer 2.0 |
| `displayName` | `<title>` | 直接映射 |
| `description` | `<description>` | 直接映射 |
| `author` | `<authors>` | 优先使用 `author`，其次 `authors` |
| `authors` | `<authors>` | 逗号分隔 → 直接映射 |
| `license` | `<license type="expression">` | 直接映射 |
| `homepage` | `<projectUrl>` | 次优先，`projectUrl` 优先 |
| `projectUrl` | `<projectUrl>` | 优先使用 |
| `tags` | `<tags>` | 逗号 → 空格分隔 |
| `dependencies` | `<dependency>` 元素 | 逗号分隔的模组名称，**不含版本** |
| `readme` | `<readme>` + 文件 | 如果是文件路径，生成 README 并映射到 `docs/README.md` |

**重要说明：**
- `info.ini` 的 `dependencies` 字段**只支持模组名称**，不支持版本范围
- 如需版本范围，必须在 `.nuspec` 中单独声明

**readme 字段特殊处理：**
- 如果 `info.ini` 中 `readme` 指定文件路径（如 `README.md`），打包工具会：
  1. 读取该文件内容
  2. 在 NuGet 包中创建 `docs/README.md`
  3. 在 `.nuspec` 中添加 `<readme>docs/README.md</readme>` 元素
- 如果 `readme` 是直接内容（非文件路径），则创建 `docs/README.md` 并写入内容

**生成的 .nuspec 示例：**
```xml
<?xml version="1.0" encoding="utf-8"?>
<package xmlns="http://schemas.microsoft.com/packaging/2013/05/nuspec.xsd">
  <metadata>
    <id>MyMod</id>
    <version>1.0.0</version>
    <title>My First Mod</title>
    <description>A simple example mod</description>
    <authors>Developer Name</authors>
    <tags>ui items utility</tags>
    <icon>icon.png</icon>
    <license type="expression">MIT</license>
    <projectUrl>https://example.com</projectUrl>
    <readme>docs/README.md</readme>
    <dependencies>
      <dependency id="OtherMod" version="1.0.0" />
      <dependency id="AnotherMod" version="[1.0.0,2.0.0)" />
    </dependencies>
  </metadata>
  <files>
    <file src="preview.png" target="icon.png" />
    <file src="**" target="content\" exclude="preview.png" />
  </files>
</package>
```

### 2.3 通配符打包规则

**推荐做法：** 使用通配符包含所有文件

```xml
<files>
  <!-- 图标到根目录 -->
  <file src="preview.png" target="icon.png" />
  <!-- 所有其他文件到 content/ -->
  <file src="**" target="content\" exclude="preview.png" />
</files>
```

**优势：**
- 自动包含所有文件和文件夹
- 添加/删除文件无需更新 `.nuspec`
- 适用于任何模组结构
- 本地化文件（description/, title/, wiki/）自动包含

**注意：** `docs/` 文件夹是 NuGet 特有的，不在 `content/` 中，用于存放包级别的文档（如 README）。

---

## 3. NuGet 包结构

### 3.1 标准包布局

```
[ModName].[Version].nupkg
├── [Content_Types].xml       # OPC MIME 类型
├── _rels/                    # OPC 关系
├── package/                  # OPC 元数据
├── [ModName].nuspec          # 包清单
├── icon.png                  # 包图标（可选）
├── docs/                     # 文档（如果存在 readme 字段）
│   └── README.md             # README 文档
└── content/                  # 模组文件
    ├── info.ini
    ├── [ModName].dll
    ├── preview.png
    └── ...                   # 其他模组文件
```

### 3.2 图标处理

| 源文件 | 包内位置 | 用途 |
|--------|----------|------|
| `preview.png` | `icon.png`（根目录） | NuGet 画廊显示 |
| `preview.png` | `content/preview.png` | 模组原始图标 |

**要求：**
- 格式：PNG
- 推荐尺寸：128x128 像素
- 最大大小：50KB

---

## 4. 安装转换规则

### 4.1 结构转换

**安装前（NuGet 包）：**
```
MyMod.1.0.0.nupkg
├── MyMod.nuspec
├── icon.png
└── content/
    ├── info.ini
    ├── MyMod.dll
    └── preview.png
```

**安装后（模组文件夹）：**
```
Mods/MyMod/
├── info.ini              # 从 content/ 提取
├── MyMod.dll             # 从 content/ 提取
├── preview.png           # 从 content/ 提取
└── .nuget/               # NuGet 标记（新增）
    └── marker.txt
```

**关键差异：**
1. `content/` 目录被移除，文件直接放入模组文件夹
2. 新增 `.nuget/marker.txt` 标记文件，用于区分 NuGet 安装的模组

### 4.2 NuGet 标记文件

**文件位置：** `.nuget/marker.txt`

**内容格式：**
```
PackageId: MyMod
PackageVersion: 1.0.0
InstalledAt: 2025-12-31T12:00:00Z
InstalledBy: NuGetModInstaller/1.0
```

**用途：**
- 区分 NuGet 安装和手动安装的模组
- 支持包管理器进行更新和卸载操作

| 源类型 | 有 `.nuget/` 标记？ |
|--------|---------------------|
| NuGet 安装 | 是 |
| 手动安装 | 否 |

### 4.3 解压映射

| 包路径 | 目标路径 |
|--------|----------|
| `content/info.ini` | `Mods/[id]/info.ini` |
| `content/*.dll` | `Mods/[id]/*.dll` |
| `content/**/*` | `Mods/[id]/**/*` |

---

## 5. 依赖管理

### 5.1 依赖声明来源

**方式 1：从 info.ini 读取（仅模组名称）**
```ini
; info.ini - 只包含模组名称，不含版本
dependencies = OtherMod, AnotherMod
```

**限制：** `info.ini` 的 `dependencies` 字段**不支持版本范围**，只能声明依赖的模组名称。

**方式 2：在 .nuspec 中显式声明（支持版本范围）**
```xml
<dependencies>
  <dependency id="OtherMod" version="1.0.0" />
  <dependency id="AnotherMod" version="[1.0.0,2.0.0)" />
</dependencies>
```

**说明：** 如果需要指定版本范围，必须在 `.nuspec` 文件中直接声明，无法通过 `info.ini` 实现。

### 5.2 NuGet 版本范围

`.nuspec` 中支持的版本范围语法（仅在 `.nuspec` 中有效）：

| 范围语法 | 含义 | 示例 |
|----------|------|------|
| `1.0.0` | 精确版本 | 只接受 1.0.0 |
| `[1.0.0,2.0.0)` | 范围 | 1.0.0 ≤ version < 2.0.0 |
| `[1.0.0,]` | 最小版本 | 1.0.0 及以上 |
| `(,1.0.0]` | 最大版本 | 1.0.0 及以下 |

**注意：** `^` 和 `~` 前缀语法**不支持**，应使用区间表示法。

---

## 6. 合规性要求

### 6.1 打包工具必须

1. 从 `info.ini` 读取所有必需和扩展元数据字段
2. 生成有效的 `.nuspec` 文件
3. 将所有模组文件放入 `content/` 目录
4. 将 `preview.png` 复制到包根目录的 `icon.png`
5. 在 `content/` 中保留原始 `info.ini`
6. 使用通配符包含所有文件（避免硬编码文件列表）

### 6.2 打包工具应该

1. 验证 `name` 为有效的 NuGet ID
2. 验证 `version` 为 SemVer 2.0 格式
3. 支持所有扩展元数据字段
4. 正确处理 `author` 和 `authors` 的优先级
5. 正确处理 `homepage` 和 `projectUrl` 的优先级
6. 支持 `readme` 字段，生成 `docs/README.md` 并在 `.nuspec` 中添加 `<readme>` 元素

### 6.3 安装工具必须

1. 将 `content/` 目录的内容解压到目标模组文件夹
2. 保留原始 `info.ini` 和所有文件结构
3. 创建 `.nuget/marker.txt` 标记文件
4. 验证安装后 `info.ini` 存在

### 6.4 安装工具应该

1. 从 `.nuspec` 读取包元数据
2. 更新前备份现有模组
3. 解析并安装依赖项
4. 支持回滚（如果安装失败）

---

## 7. 完整示例

### 7.1 源模组

**info.ini：**
```ini
name = DisplayItemValue
displayName = 显示物品价值
description = 能够显示物品的价值！！！！by xvrsl
version = 1.0.0
author = xvrsl
license = MIT
homepage = https://github.com/xvrsl

publishedFileId = 3532400883
```

**文件夹结构：**
```
DisplayItemValue/
├── info.ini
├── DisplayItemValue.dll
└── preview.png
```

### 7.2 生成的 NuGet 包

**DisplayItemValue.nuspec：**
```xml
<?xml version="1.0" encoding="utf-8"?>
<package xmlns="http://schemas.microsoft.com/packaging/2013/05/nuspec.xsd">
  <metadata>
    <id>DisplayItemValue</id>
    <version>1.0.0</version>
    <title>显示物品价值</title>
    <description>能够显示物品的价值！！！！by xvrsl</description>
    <authors>xvrsl</authors>
    <license type="expression">MIT</license>
    <projectUrl>https://github.com/xvrsl</projectUrl>
    <icon>icon.png</icon>
  </metadata>
  <files>
    <file src="preview.png" target="icon.png" />
    <file src="**" target="content\" exclude="preview.png" />
  </files>
</package>
```

**包结构：**
```
DisplayItemValue.1.0.0.nupkg
├── DisplayItemValue.nuspec
├── icon.png
└── content/
    ├── info.ini
    ├── DisplayItemValue.dll
    └── preview.png
```

### 7.3 安装结果

```
Mods/DisplayItemValue/
├── info.ini
├── DisplayItemValue.dll
├── preview.png
└── .nuget/
    └── marker.txt
```

---

## 8. 相关规范

- [游戏原生模组核心规范 v1.0](../../game-native-mod/v1.0/spec.md) - 定义模组的必需核心结构
- [游戏原生模组扩展规范 v1.0](../../game-native-mod-ext/v1.0/spec.md) - 定义扩展元数据和本地化

---

## 9. 参考文献

- [NuGet 包格式文档](https://learn.microsoft.com/en-us/nuget/reference/nuspec)
- [语义版本控制 2.0.0](https://semver.org/)

---

## 变更历史

| 版本 | 日期 | 变更 |
|------|------|------|
| 1.0 | 2025-12-31 | 初始规范 |
