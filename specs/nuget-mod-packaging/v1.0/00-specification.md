# NuGet Mod Packaging Specification v1.0

**Version:** 1.0
**Status:** Final
**Date:** 2025-12-31

---

## Abstract

This specification defines a standard for mapping game mod folders to NuGet package format. It enables the reuse of NuGet's ecosystem for mod distribution, search, version management, and dependency resolution.

---

## 1. Introduction

### 1.1 Purpose

The NuGet Mod Packaging Specification establishes conventions for:
- **Packaging** mod folders as NuGet packages
- **Installing** NuGet packages as mod folders
- **Metadata** mapping between mod formats and NuGet formats
- **Dependencies** declaration for mod-to-mod relationships

### 1.2 Scope

**In Scope:**
- Mod folder to NuGet package mapping
- NuGet package to mod folder mapping
- Metadata conventions for `info.ini` format
- Localization handling
- Asset and web UI packaging

**Out of Scope:**
- Implementation of packaging tools
- Implementation of installation tools
- NuGet server hosting
- Game-specific mod loading logic

### 1.3 Reference Implementation

This specification is based on `DisplayItemValue`, a minimal mod for "Escape from Duckov" which demonstrates the essential mod structure:
- INI-based metadata (`info.ini`)
- Single DLL assembly (`DisplayItemValue.dll`)
- Preview icon (`preview.png`)
- Steam Workshop integration (`publishedFileId`)

This mod represents the **minimum viable structure** for a NuGet-packaged mod.

---

## 2. Conventions

### 2.1 Terminology

| Term | Definition |
|------|------------|
| **Mod** | A game modification packaged according to this specification |
| **Mod Folder** | The source directory containing mod files |
| **NuGet Package** | A `.nupkg` file following NuGet conventions |
| **Target Directory** | The game mods folder where mods are installed |
| **info.ini** | The primary metadata file for mods |
| **.nuspec** | The NuGet package manifest file |

### 2.2 Requirements Keywords

- **MUST**: Absolute requirement
- **MUST NOT**: Absolute prohibition
- **SHOULD**: Recommended practice
- **SHOULD NOT**: Not recommended
- **MAY**: Optional

---

## 3. Mod Folder Structure

### 3.1 Standard Structure

```
[ModName]/
├── info.ini                          # Required: Mod metadata
├── preview.png                       # Optional: Mod icon
└── [ModName].dll                     # Required: Main mod assembly
```

**This is the minimum viable structure for a mod.**

**Note:** Mods MAY contain additional files and folders (e.g., `description/`, `title/`, `Locales/`, `Resources/`, `web/`, etc.). These are mod-specific and are automatically included via the wildcard packaging rule - they do NOT need to be explicitly listed in the specification.

### 3.2 Required Files

| File | Required | Description |
|------|----------|-------------|
| `info.ini` | **Yes** | Mod metadata |
| `[ModName].dll` | **Yes** | Main mod assembly |

### 3.3 Naming Convention: `name` MUST Match DLL

**CRITICAL:** The `name` field in `info.ini` **MUST** match the DLL filename (without extension).

| `info.ini` name | DLL filename | Valid? |
|-----------------|--------------|--------|
| `MyMod` | `MyMod.dll` | Yes |
| `MyMod` | `DifferentName.dll` | **No** - Mismatch |
| `My.Mod` | `My.Mod.dll` | Yes |
| `MyMod` | `MyMod.exe` | No - Wrong extension |

**Rationale:** This allows mod loaders to discover the correct DLL file based on the mod name, and ensures consistency between metadata and actual files.

### 3.4 Language Codes

Localization uses BCP 47 language codes:

| Code | Language |
|------|----------|
| `en` | English |
| `zh` | Chinese (Simplified) |
| `zh-hant` | Chinese (Traditional) |
| `de` | German |
| `es` | Spanish |
| `fr` | French |
| `ja` | Japanese |
| `ko` | Korean |
| `pt` | Portuguese |
| `ru` | Russian |

---

## 4. info.ini Format

### 4.1 Required Fields

| Field | Type | Description |
|-------|------|-------------|
| `name` | string | Unique mod identifier (becomes NuGet ID) **MUST match DLL filename** |
| `displayName` | string | Human-readable name |
| `description` | string | Short description |

**IMPORTANT:** The `name` field **MUST** match the mod's DLL filename (without `.dll` extension).

| info.ini | DLL file | Valid |
|----------|----------|-------|
| `name = DisplayItemValue` | `DisplayItemValue.dll` | Yes |
| `name = DisplayItemValue` | `OtherMod.dll` | **No** |

### 4.2 Optional Fields

| Field | Type | Description |
|-------|------|-------------|
| `publishedFileId` | string | Steam Workshop Published File ID |
| `version` | string | Semantic version (SemVer 2.0) |
| `tags` | string | Comma-separated search tags |
| `authors` | string | Mod author(s) |
| `license` | string | License identifier (SPDX) |
| `homepage` | string | Project homepage URL |

### 4.3 Example (Actual Format - No Sections)

**IMPORTANT:** `info.ini` uses a flat structure with NO sections/groups. All fields are top-level properties.

**Example from DisplayItemValue mod:**
```ini
name = DisplayItemValue
displayName = 显示物品价值
description = 能够显示物品的价值！！！！by xvrsl

publishedFileId = 3532400883
```

**Note:** This example shows the minimum required fields. Dependencies and other extended metadata are NOT currently supported in the `info.ini` format.

---

## 5. NuGet Package Structure

### 5.1 Package Layout

```
[ModName].[Version].nupkg
├── [Content_Types].xml               # OPC MIME types
├── _rels/                            # OPC relationships
├── package/                          # OPC metadata
├── [ModName].nuspec                  # Package manifest
├── icon.png                          # Package icon (from preview.png)
└── content/                          # Mod files
    ├── info.ini                      # Original metadata
    ├── preview.png                   # Original icon
    └── [ModName].dll                 # Mod assembly
```

**This is the minimum viable package structure.** Additional mod-specific files (e.g., `description/`, `Locales/`, `Resources/`) will be automatically included via wildcard packaging.

### 5.2 .nuspec Schema

```xml
<?xml version="1.0" encoding="utf-8"?>
<package xmlns="http://schemas.microsoft.com/packaging/2013/05/nuspec.xsd">
  <metadata>
    <id>[name from info.ini]</id>
    <version>[version from info.ini]</version>
    <title>[displayName from info.ini]</title>
    <description>[description from info.ini]</description>
    <authors>[authors from info.ini or "Unknown"]</authors>
    <tags>[tags from info.ini, comma→space]</tags>
    <icon>icon.png</icon>
    <license type="expression">[license from info.ini]</license>
    <projectUrl>[homepage from info.ini]</projectUrl>
  </metadata>
</package>
```

### 5.3 Metadata Mapping

| info.ini | .nuspec | Transformation |
|----------|---------|----------------|
| `name` | `<id>` | Must be valid NuGet ID |
| `version` | `<version>` | Must be SemVer |
| `displayName` | `<title>` | None |
| `description` | `<description>` | None (first language) |
| `tags` | `<tags>` | Comma → space |
| `authors` | `<authors>` | None |
| `license` | `<license type="expression">` | None |
| `homepage` | `<projectUrl>` | None |

---

## 6. Packaging Rules

### 6.1 Content Placement (IMPORTANT: Use Wildcards)

**PRINCIPLE:** Include ALL files from the mod folder using wildcards, NOT explicit file listings.

1. **All mod files** go into `content/` directory
2. **Folder structure** is preserved exactly
3. **`preview.png`** is copied to `icon.png` at package root
4. **`info.ini`** is preserved in `content/` (original)

### 6.2 .nuspec Files Section (Recommended)

```xml
<files>
  <!-- Icon to root (if preview.png exists) -->
  <file src="preview.png" target="icon.png" />
  <!-- ALL mod files to content/ - use wildcards! -->
  <file src="**" target="content\" exclude="preview.png" />
</files>
```

**Why use wildcards (`**`)?**
- Automatically includes ALL files and folders
- No need to update `.nuspec` when files are added/removed
- Works with any mod structure
- No risk of missing files

### 6.3 Optional Exclusions

If you need to exclude certain file types:

```xml
<file src="**" target="content\" exclude="preview.png;**/*.pdb;**/*.tmp;.git/**" />
```

**Common exclusions (optional):**
- `**/*.pdb` - Debug symbol files
- `**/*.tmp` - Temporary files
- `.git/**` - Version control metadata

**By default:** Include everything unless explicitly excluded.

### 6.4 Icon Handling

```
Source:  [ModFolder]/preview.png
Package: icon.png (root) + content/preview.png
```

**Icon requirements:**
- Format: PNG
- Recommended size: 128x128 pixels
- Maximum size: 50KB

### 6.3 Localization Handling

All three levels of localization are preserved:

| Level | Location | Format |
|-------|----------|--------|
| 1 | `info.ini` fields | INI string with `\|` delimiter |
| 2 | `content/description/*.md` | Markdown files |
| 3 | `content/title/*.md` | Markdown files |
| 4 | `content/Locales/*.csv` | CSV files |
| 5 | `content/Locales/[lang]/*.txt` | Text files |

---

## 7. Installation Rules

### 7.1 Target Directory

```
[Game Install]/
└── Mods/                              # Generic mods folder
    └── [ModName]/                     # Installation target
        ├── info.ini
        ├── [ModName].dll
        ├── .nuget/                    # NuGet marker
        │   └── marker.txt
        └── ...
```

**Target:** `$MODS_FOLDER/[id from .nuspec]/`

**Note:** Not tied to BepInEx or any specific mod loader. Simply extracts to game's Mods folder.

### 7.2 NuGet Marker File

After installation, create `.nuget/marker.txt` to distinguish NuGet-installed mods from local/manual mods:

```
.nuget/
└── marker.txt
```

**marker.txt content:**
```
PackageId: DisplayItemValue
PackageVersion: 1.0.0
InstalledAt: 2025-12-31T12:00:00Z
InstalledBy: NuGetModInstaller/1.0
```

| Source Type | Has `.nuget/` marker? |
|-------------|----------------------|
| NuGet-installed | **Yes** |
| Local/manual | No |

### 7.3 Extraction Mapping

| Package Path | Target Path |
|--------------|-------------|
| `content/` | `$MODS_FOLDER/[id]/` |
| `content/info.ini` | `$MODS_FOLDER/[id]/info.ini` |
| `content/*.dll` | `$MODS_FOLDER/[id]/*.dll` |
| `content/**/*` | `$MODS_FOLDER/[id]/**/*` |

### 7.4 Installation Steps

1. Read `.nuspec` for package ID and version
2. Determine target: `$MODS_FOLDER/[id]/`
3. Create target directory if not exists
4. Backup existing mod (if updating)
5. Extract `content/` to target
6. **Create `.nuget/marker.txt`** with package info
7. Verify `info.ini` exists

### 7.5 Metadata Preservation

- `info.ini` MUST be preserved exactly
- Folder structure MUST be preserved
- Empty directories SHOULD be preserved

---

## 8. Dependencies

### 8.1 Mod Dependencies

Declared in `.nuspec` using standard NuGet elements:

```xml
<dependencies>
  <dependency id="Other.Mod" version="1.0.0" />
  <dependency id="Game.Core.API" version="^1.0.0" />
</dependencies>
```

### 8.2 Version Ranges

| Range | Meaning |
|-------|---------|
| `1.0.0` | Exact version |
| `^1.0.0` | Compatible major version (1.x.x) |
| `~1.0.0` | Compatible minor version (1.0.x) |
| `[1.0.0,2.0.0)` | Range: 1.0.0 ≤ version < 2.0.0 |

### 8.3 Game Version

Use custom metadata or `info.ini` extension:

```ini
[game]
minVersion = 1.0.0
maxVersion = 2.0.0
```

### 8.4 Optional Dependencies

```xml
<dependencies>
  <group targetFramework=".NETFramework4.7.2-optional">
    <dependency id="Optional.Mod" version="1.0.0" />
  </group>
</dependencies>
```

---

## 9. Conformance

### 9.1 Packaging Tools MUST:

1. Read metadata from `info.ini`
2. Generate valid `.nuspec` file
3. Package all files into `content/`
4. Preserve folder structure exactly
5. Copy `preview.png` to `icon.png` if present
6. Preserve original `info.ini` in `content/`

### 9.2 Packaging Tools SHOULD:

1. Validate `name` as NuGet ID
2. Validate `version` as SemVer
3. Support optional metadata fields
4. Support dependency declarations

### 9.3 Installation Tools MUST:

1. Extract `content/` to target mod folder
2. Preserve `info.ini` from package
3. Maintain exact folder structure
4. Verify `info.ini` exists after installation

### 9.4 Installation Tools SHOULD:

1. Read `.nuspec` for package metadata
2. Backup existing mods before updating
3. Create installation manifest
4. Resolve dependencies before installation

---

## 10. Example

### 10.1 Source Mod: DisplayItemValue

**info.ini:**
```ini
name = DisplayItemValue
displayName = 显示物品价值
description = 能够显示物品的价值！！！！by xvrsl

publishedFileId = 3532400883
```

**Folder structure:**
```
DisplayItemValue/
├── info.ini
├── DisplayItemValue.dll
└── preview.png
```

### 10.2 Resulting Package

**.nuspec (using wildcard approach):**
```xml
<?xml version="1.0" encoding="utf-8"?>
<package xmlns="http://schemas.microsoft.com/packaging/2013/05/nuspec.xsd">
  <metadata>
    <id>DisplayItemValue</id>
    <version>1.0.0</version>
    <title>显示物品价值</title>
    <description>能够显示物品的价值！！！！by xvrsl</description>
    <tags></tags>
    <icon>icon.png</icon>
  </metadata>
  <files>
    <!-- Icon to root -->
    <file src="preview.png" target="icon.png" />
    <!-- ALL files to content/ -->
    <file src="**" target="content\" exclude="preview.png" />
  </files>
</package>
```

**Package structure:**
```
DisplayItemValue.1.0.0.nupkg
├── DisplayItemValue.nuspec
├── icon.png
└── content/
    ├── info.ini
    ├── DisplayItemValue.dll
    └── preview.png
```

---

## 11. References

- [NuGet Package Format Documentation](https://learn.microsoft.com/en-us/nuget/reference/nuspec)
- [Semantic Versioning 2.0.0](https://semver.org/)
- [BCP 47 Language Tags](https://tools.ietf.org/html/bcp47)
- [SPDX License List](https://spdx.org/licenses/)

---

## Change History

| Version | Date | Changes |
|---------|------|---------|
| 1.0 | 2025-12-31 | Initial specification |
