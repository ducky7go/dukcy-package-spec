# Tasks: NuGet Mod Packaging Specification

**Change ID:** `nuget-mod-packaging-spec`

---

## Task Overview

This document outlines the implementation tasks for defining the NuGet mod packaging specification.

---

## Phase 1: Research and Analysis

### Task 1.1: Analyze NuGet Package Format
- [ ] Study NuGet `.nupkg` file structure
- [ ] Understand `.nuspec` metadata schema
- [ ] Research NuGet content file conventions
- [ ] Document NuGet constraints and limitations

**Dependencies:** None
**Outputs:** Research notes on NuGet format capabilities

---

### Task 1.2: Analyze Newbe.BetterModLoader Reference Implementation
- [ ] Document the complete folder structure of Newbe.BetterModLoader
- [ ] Analyze `info.ini` metadata format and fields
- [ ] Document localization structure (description/, title/, Locales/ folders)
- [ ] Analyze asset organization (Resources/ with Font, PNG, Sounds, Vector)
- [ ] Document web UI components (web/ folder with HTML/CSS/JS)
- [ ] Identify the DLL file and its role
- [ ] Identify edge cases specific to this structure

**Dependencies:** None
**Outputs:** Complete documentation of Newbe.BetterModLoader structure

---

### Task 1.3: Review Similar Specifications
- [ ] Research existing package-to-folder mapping standards
- [ ] Review Unity Package Manager format
- [ ] Review Steam Workshop publishing format (relevant due to `publishedFileId`)
- [ ] Review existing mod loaders' metadata formats
- [ ] Document applicable patterns

**Dependencies:** None
**Outputs:** Comparative analysis document

---

## Phase 2: Specification Design

### Task 2.1: Define Metadata Mapping
- [ ] Map `info.ini` fields to NuGet metadata (`name` → `id`, `version` → `version`, etc.)
- [ ] Handle `displayName` field (localizable display name vs. package ID)
- [ ] Handle `description` field (may be markdown formatted)
- [ ] Map `tags` to NuGet tags
- [ ] Define handling for `publishedFileId` (Steam Workshop ID)
- [ ] Define author/credits mapping
- [ ] Define icon mapping (`preview.png` → NuGet icon)

**Dependencies:** Task 1.1, Task 1.2
**Outputs:** Metadata mapping specification section

---

### Task 2.2: Define Content Mapping (Packaging)
- [ ] Define source folder structure conventions based on Newbe.BetterModLoader
- [ ] Specify content file placement in `.nupkg` (Resources/, Locales/, web/, DLL)
- [ ] Define how to handle localization files (description/*.md, title/*.md, Locales/*.csv)
- [ ] Define how to handle asset subdirectories (Resources/Font, Resources/PNG, Resources/Sounds, Resources/Vector)
- [ ] Define handling for web UI files (HTML, CSS, JS)
- [ ] Define handling for DLL files (main mod code)
- [ ] Specify content files vs. build files in NuGet terminology

**Dependencies:** Task 1.1, Task 1.2
**Outputs:** Packaging mapping specification section

---

### Task 2.3: Define Content Mapping (Installation)
- [ ] Define extraction target resolution (game mods folder)
- [ ] Specify which package contents map to which mod locations
- [ ] Define how to preserve `info.ini` during installation
- [ ] Define how to handle overwrites during installation
- [ ] Define uninstallation behavior (what gets removed)
- [ ] Ensure localization files are correctly placed

**Dependencies:** Task 2.2
**Outputs:** Installation mapping specification section

---

### Task 2.4: Define Dependency Declaration
- [ ] Specify how to add NuGet dependencies (mod-to-mod dependencies)
- [ ] Define game version dependency format
- [ ] Define how to represent `publishedFileId` in NuGet package
- [ ] Define conflict/incompatibility declaration
- [ ] Define optional dependencies

**Dependencies:** Task 2.1
**Outputs:** Dependency specification section

---

## Phase 3: Documentation

### Task 3.1: Write Core Specification Document
- [ ] Compile all mapping rules into a cohesive document
- [ ] Add formal specification language (RFC-style or similar)
- [ ] Include examples for each rule
- [ ] Define conformance requirements

**Dependencies:** Task 2.1, Task 2.2, Task 2.3, Task 2.4
**Outputs:** Core specification markdown document

---

### Task 3.2: Create Reference Examples
- [ ] Document Newbe.BetterModLoader as the reference example
- [ ] Show complete folder structure of Newbe.BetterModLoader
- [ ] Show resulting `.nuspec` file from Newbe.BetterModLoader's `info.ini`
- [ ] Show resulting `.nupkg` structure
- [ ] Document installation process step-by-step
- [ ] Include edge case examples (localization files, web UI, DLL files)

**Dependencies:** Task 3.1
**Outputs:** Example documentation directory with Newbe.BetterModLoader reference

---

### Task 3.3: Create Validation Checklist
- [ ] List requirements for valid mod packages
- [ ] Create validation criteria for packaging tools
- [ ] Create validation criteria for installation tools
- [ ] Define test cases for specification compliance

**Dependencies:** Task 3.1
**Outputs:** Validation checklist document

---

## Phase 4: Review and Refinement

### Task 4.1: Internal Review
- [ ] Review specification for completeness
- [ ] Check for ambiguities or contradictions
- [ ] Verify all mappings are bidirectional (pack + install)
- [ ] Ensure compatibility with NuGet tooling

**Dependencies:** Task 3.1, Task 3.2, Task 3.3
**Outputs:** Review feedback and revisions

---

### Task 4.2: External Feedback (Optional)
- [ ] Share specification with potential tool authors
- [ ] Gather feedback on usability
- [ ] Identify missing scenarios
- [ ] Refine based on feedback

**Dependencies:** Task 4.1
**Outputs:** Revised specification

---

## Phase 5: Finalization

### Task 5.1: Finalize Specification
- [ ] Incorporate all review feedback
- [ ] Complete all examples
- [ ] Finalize document structure
- [ ] Create versioned release of specification

**Dependencies:** Task 4.2
**Outputs:** Final specification v1.0

---

### Task 5.2: Create Migration Guide (if applicable)
- [ ] Document any migration from pre-spec formats
- [ ] Provide transition guidelines
- [ ] Document any breaking changes

**Dependencies:** Task 5.1
**Outputs:** Migration guide (if needed)

---

## Task Summary

| Phase | Tasks | Status |
|-------|-------|--------|
| Phase 1: Research and Analysis | 3 | Pending |
| Phase 2: Specification Design | 4 | Pending |
| Phase 3: Documentation | 3 | Pending |
| Phase 4: Review and Refinement | 2 | Pending |
| Phase 5: Finalization | 2 | Pending |

**Total Tasks:** 14
**Estimated Phases:** 5
