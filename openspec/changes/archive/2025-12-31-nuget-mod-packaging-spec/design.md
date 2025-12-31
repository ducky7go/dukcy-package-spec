# Design: NuGet Mod Packaging Specification

**Change ID:** `nuget-mod-packaging-spec`

---

## Overview

This document describes the architectural design decisions for the NuGet mod packaging specification. It elaborates on the mapping strategies and technical considerations based on the `Newbe.BetterModLoader` reference implementation.

---

## Architecture

```
┌─────────────────────────────────────────────────────────────────┐
│                    Mod Folder (Newbe.BetterModLoader)            │
│  ┌──────────────┐  ┌──────────────┐  ┌──────────────┐           │
│  │ info.ini     │  │preview.png   │  │ .dll files   │           │
│  │ (metadata)   │  │ (icon)       │  │ (code)       │           │
│  └──────────────┘  └──────────────┘  └──────────────┘           │
│  ┌──────────────┐  ┌──────────────┐  ┌──────────────┐           │
│  │ description/ │  │ title/       │  │ Locales/     │           │
│  │ (*.md i18n)  │  │ (*.md i18n)  │  │ (*.csv i18n) │           │
│  └──────────────┘  └──────────────┘  └──────────────┘           │
│  ┌──────────────┐  ┌──────────────┐                          │
│  │ Resources/   │  │ web/         │                          │
│  │ (assets)     │  │ (UI)         │                          │
│  └──────────────┘  └──────────────┘                          │
└────────────────────────────┬────────────────────────────────────┘
                             │
                             ▼
┌─────────────────────────────────────────────────────────────────┐
│                    Packaging Process                             │
│  ┌─────────────────────────────────────────────────────────┐    │
│  │  1. Read mod metadata from info.ini                      │    │
│  │  2. Generate .nuspec with mapped metadata                │    │
│  │  3. Package preview.png as NuGet icon                    │    │
│  │  4. Package all content files into content/ directory    │    │
│  │  5. Create .nupkg file                                    │    │
│  └─────────────────────────────────────────────────────────┘    │
└────────────────────────────┬────────────────────────────────────┘
                             │
                             ▼
┌─────────────────────────────────────────────────────────────────┐
│                      NuGet Package (.nupkg)                      │
│  ┌──────────────┐  ┌──────────────┐  ┌──────────────┐           │
│  │ .nuspec      │  │  content/    │  │  icon.png    │           │
│  │ (metadata)   │  │  (files)     │  │  (from       │           │
│  │              │  │              │  │  preview.png)│           │
│  └──────────────┘  └──────────────┘  └──────────────┘           │
└────────────────────────────┬────────────────────────────────────┘
                             │
                             ▼
┌─────────────────────────────────────────────────────────────────┐
│                    Installation Process                          │
│  ┌─────────────────────────────────────────────────────────┐    │
│  │  1. Extract .nupkg                                       │    │
│  │  2. Read .nuspec metadata                                │    │
│  │  3. Extract content/ to target game mods directory       │    │
│  │  4. Preserve info.ini for runtime mod loading            │    │
│  │  5. Preserve folder hierarchy (Resources/, Locales/, web/)│    │
│  └─────────────────────────────────────────────────────────┘    │
└────────────────────────────┬────────────────────────────────────┘
                             │
                             ▼
┌─────────────────────────────────────────────────────────────────┐
│                    Installed Mod Folder                          │
│  ┌──────────────┐  ┌──────────────┐  ┌──────────────┐           │
│  │ info.ini     │  │description/  │  │ Locales/     │           │
│  │ (preserved)  │  │ (localized)  │  │ (localized)  │           │
│  └──────────────┘  └──────────────┘  └──────────────┘           │
│  ┌──────────────┐  ┌──────────────┐                           │
│  │ Resources/   │  │ .dll files   │                           │
│  │ (restored)   │  │ (restored)   │                           │
│  └──────────────┘  └──────────────┘                           │
└─────────────────────────────────────────────────────────────────┘
```

---

## Design Decisions

### Decision 1: Use NuGet Content Files

**Context:** Need to store mod files in NuGet package while preserving structure.

**Options:**
1. Use `content/` directory (legacy NuGet content files)
2. Use `contentFiles/` directory (modern NuGet content files)
3. Use custom directory with build script

**Decision:** Use `content/` directory

**Rationale:**
- `content/` is simpler and more widely supported
- Preserves folder structure automatically
- Doesn't require complex `contentFiles` configuration
- Works with standard NuGet extraction

**Trade-offs:**
- Less granular control than `contentFiles`
- May require tooling to filter files during extraction

---

### Decision 2: Metadata File Convention

**Context:** Need a standard way to identify and read mod metadata.

**Options:**
1. Require `mod.json` or `mod.yaml` in mod root
2. Support `info.ini` format (INI-based, used by Newbe.BetterModLoader)
3. Support multiple metadata formats with detection
4. Use `.nuspec` as sole metadata source

**Decision:** Support `info.ini` as primary format, with optional support for other formats

**Rationale:**
- `info.ini` is the established format in Newbe.BetterModLoader
- INI format is simple and widely supported
- Fields like `publishedFileId` (Steam Workshop) are already defined
- Clear expectation based on existing implementation
- JSON/YAML support can be added as optional extensions

**Trade-offs:**
- INI format is less structured than JSON
- May need migration path for other formats

**Reference info.ini format:**
```ini
name = Newbe.BetterModLoader
displayName = Better Mod Loader | 更好的 mod 加载器
description = Better Mod Loader | 更好的 mod 加载器
version = 1.3.3
tags = Utility, Quality of Life
publishedFileId = 3596571766
```

---

### Decision 3: Dependency Mapping Strategy

**Context:** Mods need to declare dependencies on other mods and game versions.

**Options:**
1. Use NuGet's standard `<dependency>` elements
2. Use custom metadata with tooling interpretation
3. Hybrid: standard dependencies + custom metadata

**Decision:** Hybrid approach

**Rationale:**
- Standard NuGet dependencies for mod-to-mod dependencies
- Custom metadata for game version constraints
- Leverages NuGet's dependency resolution
- Allows game-specific versioning schemes

**Trade-offs:**
- More complex than pure approach
- Requires documentation of both systems

---

### Decision 4: Installation Tracking

**Context:** Need to track which files belong to which installed mod for uninstallation.

**Options:**
1. Write manifest file during installation
2. Use NuGet's nupkg metadata tracking
3. Track in separate database/registry

**Decision:** Write manifest file to `.metadata/` directory in game folder

**Rationale:**
- Simple and self-contained
- Survives package deletion
- Easy to inspect and debug
- Works without external services

**Trade-offs:**
- Creates additional files in game directory
- Requires cleanup on uninstall

---

### Decision 5: Icon and Screenshots

**Context:** Want to support visual assets for mod display.

**Options:**
1. Use NuGet's icon file support
2. Embed images in metadata
3. Use external URL references

**Decision:** Use `preview.png` as NuGet icon

**Rationale:**
- `preview.png` is the established naming in Newbe.BetterModLoader
- Icon is standard NuGet feature
- Simple rename from `preview.png` to `icon.png` during packaging
- Compatible with NuGet galleries

**Trade-offs:**
- Requires file rename during packaging process
- NuGet icon has size limitations

---

### Decision 6: Localization Handling

**Context:** Mods support multiple languages with separate files.

**Options:**
1. Embed all localizations in NuGet metadata
2. Package localization files as content
3. Use external localization services

**Decision:** Package localization files as content (preserve folder structure)

**Rationale:**
- Preserves existing structure from Newbe.BetterModLoader
- Three localization folders: `description/`, `title/`, `Locales/`
- Allows runtime mod loading to read files directly
- No transformation needed during packaging

**Trade-offs:**
- Larger package size with all languages included
- Runtime must read from file system

---

### Decision 7: Web UI Components

**Context:** Some mods include web-based UI components.

**Options:**
1. Package web files as content
2. Embed web assets in DLL as resources
3. Serve from external CDN

**Decision:** Package web files as content (preserve `web/` folder)

**Rationale:**
- Preserves existing structure from Newbe.BetterModLoader
- Allows mod to update web UI without recompilation
- Simple packaging with no transformation

---

## File Structure Convention

### Source Mod Folder (based on Newbe.BetterModLoader)

```
Newbe.BetterModLoader/
├── info.ini              # Required metadata (INI format)
├── preview.png           # Optional icon (renamed to icon.png in package)
├── Newbe.BetterModLoader.dll  # Main mod code
├── description/          # Localized descriptions (markdown)
│   ├── en.md
│   ├── zh.md
│   ├── de.md
│   └── ... (other languages)
├── title/                # Localized titles (markdown)
│   ├── en.md
│   ├── zh.md
│   └── ... (other languages)
├── Locales/              # Localization CSV files
│   ├── en.csv
│   ├── zh.csv
│   └── ... (other languages)
│   └── en/               # Per-language text files
│       └── bml_*.txt
├── Resources/            # Mod assets
│   ├── Font/             # Font files
│   ├── PNG/              # Image assets
│   ├── Sounds/           # Audio files
│   └── Vector/           # Vector graphics
└── web/                  # Web UI components
    ├── index.html
    ├── styles.css
    └── js/
```

### NuGet Package Structure

```
Newbe.BetterModLoader.1.3.3.nupkg
├── [Content Types]
├── _rels/
├── package/
│   └── services/
│       └── metadata/
│           └── core-properties/
│               └── ...psmdcp
├── Newbe.BetterModLoader.nuspec  # Package metadata (from info.ini)
├── icon.png                      # Package icon (from preview.png)
└── content/                      # Mod files (preserves structure)
    ├── info.ini                  # Original metadata preserved
    ├── Newbe.BetterModLoader.dll # Main mod code
    ├── description/              # Localized descriptions
    │   ├── en.md
    │   └── ...
    ├── title/                    # Localized titles
    │   ├── en.md
    │   └── ...
    ├── Locales/                  # Localization files
    │   ├── en.csv
    │   └── ...
    ├── Resources/                # Mod assets
    │   ├── Font/
    │   ├── PNG/
    │   ├── Sounds/
    │   └── Vector/
    └── web/                      # Web UI
        ├── index.html
        └── ...
```

---

## Metadata Mapping

| info.ini Field | NuGet Field | Notes |
|----------------|-------------|-------|
| `name` | `id` | Must be valid NuGet ID (no spaces, special chars) |
| `displayName` | `title` (or mapped to `id` if not present) | Localizable display name |
| `description` | `description` | Plain text, may reference description/*.md files |
| `version` | `version` | SemVer format required (e.g., 1.3.3) |
| `tags` | `tags` | Comma-separated tags (e.g., "Utility Quality of Life") |
| `publishedFileId` | Custom metadata | Store in NuGet metadata as `publishedFileId` |
| (author) | `authors` | Not in info.ini, may need to be added |
| (license) | `license` | Not in info.ini, may need to be added |
| (homepage) | `projectUrl` | Not in info.ini, may need to be added |

### Example .nuspec Generation

**Input (info.ini):**
```ini
name = Newbe.BetterModLoader
displayName = Better Mod Loader | 更好的 mod 加载器
description = Better Mod Loader | 更好的 mod 加载器
version = 1.3.3
tags = Utility, Quality of Life
publishedFileId = 3596571766
```

**Output (.nuspec):**
```xml
<?xml version="1.0" encoding="utf-8"?>
<package xmlns="http://schemas.microsoft.com/packaging/2013/05/nuspec.xsd">
  <metadata>
    <id>Newbe.BetterModLoader</id>
    <version>1.3.3</version>
    <title>Better Mod Loader | 更好的 mod 加载器</title>
    <description>Better Mod Loader | 更好的 mod 加载器</description>
    <tags>Utility Quality of Life</tags>
    <icon>icon.png</icon>
  </metadata>
</package>
```

---

## Open Questions

1. How do we handle Steam Workshop integration with `publishedFileId` in NuGet context?
2. Should we support compression of package contents for large asset folders?
3. How do we handle mods that modify existing game files (patching)?
4. Should there be a manifest format for multi-file mods?
5. How do we handle metadata fields not present in `info.ini` (author, license, homepage)?
6. Should we define extension fields for `info.ini` to include missing NuGet metadata?

---

## Future Considerations

1. **Signed Packages**: Support for package signing and verification
2. **Delta Updates**: Partial updates for large mods
3. **Virtual File Systems**: Support for mods that VFS their assets
4. **Loading Order**: Specifying mod load order dependencies
5. **Multiple Metadata Formats**: Support for `mod.json` or `mod.yaml` alongside `info.ini`
6. **Steam Workshop Sync**: Automatic synchronization between NuGet and Steam Workshop
