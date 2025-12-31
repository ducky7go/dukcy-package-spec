# Proposal: NuGet Mod Packaging Specification

**Change ID:** `nuget-mod-packaging-spec`
**Status:** Draft
**Created:** 2025-12-31

---

## Overview

Define a specification for mapping game mod folders to NuGet package format, enabling reuse of the NuGet ecosystem for mod distribution and management.

## Background

Game mods typically exist as folder structures containing game assets, scripts, and configuration files. This project aims to standardize how these folders are packaged as NuGet packages, allowing us to leverage:

- NuGet's search and discovery mechanisms
- Package information and metadata display
- Version management and dependency resolution
- Icon and screenshot support
- Distribution through NuGet servers

This specification is based on the real-world example of `Newbe.BetterModLoader` (a mod for "Escape from Duckov"), which demonstrates a typical mod structure with:
- `info.ini` metadata file containing mod information
- Localized descriptions and titles in multiple languages
- Asset resources (fonts, images, sounds, vectors)
- Localization files (CSV format)
- Web UI components
- Main mod DLL file

## Problem Statement

Currently, there is no standardized specification for:

1. **Packaging Mapping**: How to map a mod's folder structure into a NuGet package
2. **Installation Mapping**: How to extract and install a NuGet package back to the original mod folder structure

Without this specification, tools cannot reliably create or consume mod packages, and the ecosystem cannot benefit from NuGet's capabilities.

## Proposed Solution

### 1. Packaging Specification

Define rules for mapping mod folders to NuGet packages based on the `Newbe.BetterModLoader` reference implementation:

- **Metadata File Support**: Support `info.ini` format (INI-based metadata with fields like `name`, `displayName`, `description`, `version`, `tags`, `publishedFileId`)
- **Folder Structure Mapping**: Specify how mod files are organized within the NuGet package
- **Localization Handling**: Define mapping for localized content (`description/`, `title/`, `Locales/` folders)
- **Asset Management**: Define handling for resource folders (`Resources/` with fonts, images, sounds, vectors)
- **Web UI Components**: Define packaging for web-based UI files

### 2. Installation Specification

Define rules for extracting NuGet packages to target folders:

- **Extraction Rules**: Specify which package contents go to which target locations
- **Path Resolution**: Define how to resolve paths during installation
- **Metadata Preservation**: Ensure `info.ini` is preserved for runtime mod loading
- **Validation**: Ensure installed structure matches the original mod structure

## Scope

### In Scope

- Specification for packaging mod folders as NuGet packages
- Specification for installing NuGet packages as mod folders
- Metadata mapping conventions
- File organization conventions

### Out of Scope

- Implementation of packaging tools (future work)
- Implementation of installation tools (future work)
- NuGet server hosting infrastructure
- Mod-specific loading logic

## Impact

### Benefits

- **Search & Discovery**: Leverage NuGet's package search functionality
- **Rich Metadata**: Display comprehensive mod information via NuGet metadata
- **Version Management**: Automatic version comparison and dependency resolution
- **Visual Assets**: Support for icons and screenshots through NuGet conventions
- **Distribution**: Use existing NuGet servers for package hosting

### Risks

- **Complexity**: Mapping arbitrary folder structures to NuGet's conventions may be complex
- **Compatibility**: Must ensure compatibility with existing NuGet tools and consumers
- **Adoption**: Requires community adoption to be effective

## Success Criteria

1. A clear, unambiguous specification document exists
2. The specification covers both packaging and installation scenarios
3. Reference examples based on `Newbe.BetterModLoader` demonstrate the mapping rules
4. The specification is compatible with standard NuGet tooling
5. The specification supports INI-based metadata format
6. The specification handles multi-language localization correctly

## Related Changes

None (this is the foundational specification)

## References

- [NuGet Package Format Documentation](https://learn.microsoft.com/en-us/nuget/reference/nuspec)
