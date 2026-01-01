# Project Context

## Purpose

This repository contains specifications for game mod packaging and distribution, specifically for "Escape from Duckov" (《逃离达科夫》) game mods. The project defines standards for:

1. **Native mod folder structure** - The core mod format with `info.ini`, DLL files, and preview images
2. **Mod extensions** - Optional features like localization, metadata fields, and wiki documentation
3. **NuGet packaging** - Converting native mods to NuGet packages for distribution via NuGet ecosystem

## Tech Stack

- **Documentation**: Markdown (.md) specifications
- **Metadata Format**: INI files (info.ini)
- **Package Format**: NuGet (.nupkg) with .nuspec manifests
- **Localization**: BCP 47 language codes
- **Versioning**: Semantic Versioning 2.0.0 (SemVer)

## Project Conventions

### Code Style

- All specification files are written in Markdown
- Chinese and English bilingual documentation
- Consistent section numbering and formatting across specs
- Tables for field definitions, mappings, and examples

### Architecture Patterns

The specification follows a layered architecture:

```
游戏原生模组核心规范 (game-native-mod)
        ↓
游戏原生模组扩展规范 (game-native-mod-ext)
        ↓
NuGet 模组打包规范 (nuget-mod-packaging)
```

Each spec builds upon the previous layer, maintaining separation of concerns.

### Testing Strategy

Not applicable - this is a specification repository.

### Git Workflow

- Main branch: `main`
- Commit messages follow conventional format
- Spec changes go through OpenSpec proposal process (see `openspec/` folder)

## Domain Context

### Game: Escape from Duckov (《逃离达科夫》)

A game that supports native C# modding through DLL-based mods.

### Mod Structure

A minimal mod consists of:
- `info.ini` - Mod metadata (name, displayName, description, version, etc.)
- `[ModName].dll` - Main mod assembly
- `preview.png` - Mod preview icon (128x128 PNG recommended)

### Key Concepts

- **BCP 47 Language Codes**: Used for localization (e.g., `zh` for Chinese, `en` for English)
- **Language Fallback Order**: Preferred language → zh → en → first available
- **NuGet Content Directory**: Mod files are placed in `content/` within .nupkg
- **NuGet Marker File**: `.nuget/marker.txt` identifies NuGet-installed mods

## Important Constraints

- `info.ini` `name` field MUST match the DLL filename (without extension)
- `dependencies` field in `info.ini` only supports mod names, not version ranges
- Version ranges can only be specified in `.nuspec` files
- All text files must use UTF-8 encoding
- PNG preview images recommended: 128x128 pixels, max 50KB

## External Dependencies

- [NuGet Package Format](https://learn.microsoft.com/en-us/nuget/reference/nuspec)
- [Semantic Versioning 2.0.0](https://semver.org/)
- [SPDX License List](https://spdx.org/licenses/)
- [BCP 47 Language Tags](https://tools.ietf.org/html/bcp47)
- [Escape from Duckov Official Mod Documentation](https://github.com/xvrsl/duckov_modding)
- [Newbe.BetterModLoader](https://github.com/Newbe36524/Newbe.BetterModLoader)
