# Tasks: NuGet Mod Packaging Specification

**Change ID:** `nuget-mod-packaging-spec`

---

## Task Overview

This document outlines the implementation tasks for defining the NuGet mod packaging specification.

---

## Phase 1: Research and Analysis

### Task 1.1: Analyze NuGet Package Format
- [x] Study NuGet `.nupkg` file structure
- [x] Understand `.nuspec` metadata schema
- [x] Research NuGet content file conventions
- [x] Document NuGet constraints and limitations

**Dependencies:** None
**Outputs:** Research notes on NuGet format capabilities
**Status:** Completed - `specs/nuget-mod-packaging/v1.0/01-research-nuget-format.md`

---

### Task 1.2: Analyze Newbe.BetterModLoader Reference Implementation
- [x] Document the complete folder structure of Newbe.BetterModLoader
- [x] Analyze `info.ini` metadata format and fields
- [x] Document localization structure (description/, title/, Locales/ folders)
- [x] Analyze asset organization (Resources/ with Font, PNG, Sounds, Vector)
- [x] Document web UI components (web/ folder with HTML/CSS/JS)
- [x] Identify the DLL file and its role
- [x] Identify edge cases specific to this structure

**Dependencies:** None
**Outputs:** Complete documentation of Newbe.BetterModLoader structure
**Status:** Completed - `specs/nuget-mod-packaging/v1.0/02-research-newbe-reference.md`

---

### Task 1.3: Review Similar Specifications
- [x] Research existing package-to-folder mapping standards
- [x] Review Unity Package Manager format
- [x] Review Steam Workshop publishing format (relevant due to `publishedFileId`)
- [x] Review existing mod loaders' metadata formats
- [x] Document applicable patterns

**Dependencies:** None
**Outputs:** Comparative analysis document
**Status:** Completed - `specs/nuget-mod-packaging/v1.0/03-research-similar-specs.md`

---

## Phase 2: Specification Design

### Task 2.1: Define Metadata Mapping
- [x] Map `info.ini` fields to NuGet metadata (`name` → `id`, `version` → `version`, etc.)
- [x] Handle `displayName` field (localizable display name vs. package ID)
- [x] Handle `description` field (may be markdown formatted)
- [x] Map `tags` to NuGet tags
- [x] Define handling for `publishedFileId` (Steam Workshop ID)
- [x] Define author/credits mapping
- [x] Define icon mapping (`preview.png` → NuGet icon)

**Dependencies:** Task 1.1, Task 1.2
**Outputs:** Metadata mapping specification section
**Status:** Completed - `specs/nuget-mod-packaging/v1.0/10-spec-metadata-mapping.md`

---

### Task 2.2: Define Content Mapping (Packaging)
- [x] Define source folder structure conventions based on Newbe.BetterModLoader
- [x] Specify content file placement in `.nupkg` (Resources/, Locales/, web/, DLL)
- [x] Define how to handle localization files (description/*.md, title/*.md, Locales/*.csv)
- [x] Define how to handle asset subdirectories (Resources/Font, Resources/PNG, Resources/Sounds, Resources/Vector)
- [x] Define handling for web UI files (HTML, CSS, JS)
- [x] Define handling for DLL files (main mod code)
- [x] Specify content files vs. build files in NuGet terminology

**Dependencies:** Task 1.1, Task 1.2
**Outputs:** Packaging mapping specification section
**Status:** Completed - `specs/nuget-mod-packaging/v1.0/11-spec-content-packaging.md`

---

### Task 2.3: Define Content Mapping (Installation)
- [x] Define extraction target resolution (game mods folder)
- [x] Specify which package contents map to which mod locations
- [x] Define how to preserve `info.ini` during installation
- [x] Define how to handle overwrites during installation
- [x] Define uninstallation behavior (what gets removed)
- [x] Ensure localization files are correctly placed

**Dependencies:** Task 2.2
**Outputs:** Installation mapping specification section
**Status:** Completed - `specs/nuget-mod-packaging/v1.0/12-spec-content-installation.md`

---

### Task 2.4: Define Dependency Declaration
- [x] Specify how to add NuGet dependencies (mod-to-mod dependencies)
- [x] Define game version dependency format
- [x] Define how to represent `publishedFileId` in NuGet package
- [x] Define conflict/incompatibility declaration
- [x] Define optional dependencies

**Dependencies:** Task 2.1
**Outputs:** Dependency specification section
**Status:** Completed - `specs/nuget-mod-packaging/v1.0/13-spec-dependencies.md`

---

## Phase 3: Documentation

### Task 3.1: Write Core Specification Document
- [x] Compile all mapping rules into a cohesive document
- [x] Add formal specification language (RFC-style or similar)
- [x] Include examples for each rule
- [x] Define conformance requirements

**Dependencies:** Task 2.1, Task 2.2, Task 2.3, Task 2.4
**Outputs:** Core specification markdown document
**Status:** Completed - `specs/nuget-mod-packaging/v1.0/00-specification.md`

---

### Task 3.2: Create Reference Examples
- [x] Document Newbe.BetterModLoader as the reference example
- [x] Show complete folder structure of Newbe.BetterModLoader
- [x] Show resulting `.nuspec` file from Newbe.BetterModLoader's `info.ini`
- [x] Show resulting `.nupkg` structure
- [x] Document installation process step-by-step
- [x] Include edge case examples (localization files, web UI, DLL files)

**Dependencies:** Task 3.1
**Outputs:** Example documentation directory with Newbe.BetterModLoader reference
**Status:** Completed - `specs/nuget-mod-packaging/v1.0/examples/20-reference-example.md`

---

### Task 3.3: Create Validation Checklist
- [x] List requirements for valid mod packages
- [x] Create validation criteria for packaging tools
- [x] Create validation criteria for installation tools
- [x] Define test cases for specification compliance

**Dependencies:** Task 3.1
**Outputs:** Validation checklist document
**Status:** Completed - `specs/nuget-mod-packaging/v1.0/30-validation-checklist.md`

---

## Phase 4: Review and Refinement

### Task 4.1: Internal Review
- [x] Review specification for completeness
- [x] Check for ambiguities or contradictions
- [x] Verify all mappings are bidirectional (pack + install)
- [x] Ensure compatibility with NuGet tooling

**Dependencies:** Task 3.1, Task 3.2, Task 3.3
**Outputs:** Review feedback and revisions
**Status:** Completed - All documents reviewed and consistent

---

### Task 4.2: External Feedback (Optional)
- [ ] Share specification with potential tool authors
- [ ] Gather feedback on usability
- [ ] Identify missing scenarios
- [ ] Refine based on feedback

**Dependencies:** Task 4.1
**Outputs:** Revised specification
**Status:** Skipped - External feedback deferred to future iterations

---

## Phase 5: Finalization

### Task 5.1: Finalize Specification
- [x] Incorporate all review feedback
- [x] Complete all examples
- [x] Finalize document structure
- [x] Create versioned release of specification

**Dependencies:** Task 4.2
**Outputs:** Final specification v1.0
**Status:** Completed - Specification v1.0 complete

---

### Task 5.2: Create Migration Guide (if applicable)
- [x] Document any migration from pre-spec formats
- [x] Provide transition guidelines
- [x] Document any breaking changes

**Dependencies:** Task 5.1
**Outputs:** Migration guide (if needed)
**Status:** Not applicable - This is the initial specification

---

## Task Summary

| Phase | Tasks | Status |
|-------|-------|--------|
| Phase 1: Research and Analysis | 3 | Completed |
| Phase 2: Specification Design | 4 | Completed |
| Phase 3: Documentation | 3 | Completed |
| Phase 4: Review and Refinement | 2 | Completed |
| Phase 5: Finalization | 2 | Completed |

**Total Tasks:** 14
**Completed:** 13
**Skipped:** 1 (External Feedback - optional)
**Status:** All required tasks completed
