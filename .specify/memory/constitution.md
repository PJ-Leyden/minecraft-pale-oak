<!-- Sync Impact Report
Version change: (none) → 1.0.0 (initial creation)
Modified principles: N/A — initial creation
Added sections: Core Principles, Compatibility & Integration,
  Development Workflow, Governance
Removed sections: N/A
Templates requiring updates:
  ✅ .specify/templates/plan-template.md — Constitution Check gates align with all five
     principles; no structural changes required
  ✅ .specify/templates/spec-template.md — Mandatory sections unchanged; Functional
     Requirements and User Scenarios cover mod content scope adequately
  ✅ .specify/templates/tasks-template.md — Task categories (Setup, Foundational, User
     Story phases, Polish) map cleanly to mod development workflow; no changes required
  ℹ️  .specify/templates/commands/ — No command files found; no updates required
Follow-up TODOs: None — all placeholders resolved
-->

# Pale Oak NeoForge Mod Constitution

## Core Principles

### I. Vanilla Consistency

All pale oak content — blocks, items, sounds, particles, and world-generation features —
MUST follow vanilla Minecraft 1.21.1 conventions and patterns. Block states, item models,
textures, and sound events MUST mirror the naming, structure, and behavior of existing
vanilla wood types (e.g., oak, birch, spruce). Divergence from vanilla patterns is
PROHIBITED unless explicitly documented with a rationale in the relevant spec.

**Rationale**: Players expect pale oak to feel native to the game. Consistency with vanilla
reduces support burden and ensures compatibility with resource packs that follow vanilla
naming conventions.

### II. NeoForge API First

All content registration MUST use NeoForge's `DeferredRegister` and event bus system.
Direct manipulation of Minecraft registries, reflection into private fields, or bypassing
NeoForge-provided lifecycle hooks is PROHIBITED. Where NeoForge does not yet provide an
API for a specific capability, the bypass MUST be documented with a `// TODO: Remove when
NeoForge API available` comment and tracked as a task.

**Rationale**: NeoForge API usage ensures forward compatibility with NeoForge updates and
interoperability with the broader mod ecosystem. Workarounds that bypass APIs accumulate
technical debt that is hard to locate without explicit tracking.

### III. Data-Driven Design

Block states, block models, item models, loot tables, crafting recipes, tags, advancement
criteria, and language entries MUST be defined as JSON/SNBT data files under
`src/main/resources/`. Hardcoding values that Minecraft's data-pack or resource-pack system
can express as data files is PROHIBITED.

**Rationale**: Data files are overridable by resource packs and data packs, enabling
community customization. They are also easier to audit, diff, and validate than hardcoded
Java constants scattered across the codebase.

### IV. Minimal Scope

The mod MUST add only the content required for a complete, functional pale oak wood type:
logs, stripped logs, wood, stripped wood, planks, slabs, stairs, fences, fence gates,
doors, trapdoors, pressure plates, buttons, signs, hanging signs, boats, chest boats, and
leaves — plus any supporting world-generation (tree feature, configured feature, placed
feature, biome modifier). Additions outside this scope (new mechanics, non-pale-oak
content, cross-mod integrations) require an explicit spec amendment and MUST NOT land on
the main branch without one.

**Rationale**: Scope creep in a focused content mod dilutes maintenance effort and
increases the surface area for bugs and compatibility issues. A tight scope also makes the
mod easier to understand and contribute to.

### V. Build Integrity

The mod MUST compile and pass `./gradlew build` without errors or warnings treated as
errors before any commit reaches the main branch. All registered blocks and items MUST be
reachable in a test world without crashes, missing textures, or missing models. Crashes-on-
load or missing-texture reports in CI or manual testing MUST be resolved before merging.

**Rationale**: A mod that crashes or displays broken assets reflects poorly on the project
and breaks the experience for players. Keeping the build green at all times makes
regressions easy to pinpoint.

## Compatibility & Integration

This mod targets **Minecraft 1.21.1** with **NeoForge** (the primary modloader for that
version). The following compatibility constraints apply:

- The mod MUST function correctly in both single-player and multiplayer (dedicated server)
  environments. Server-side registration and client-side rendering MUST be separated
  correctly using `@OnlyIn(Dist.CLIENT)` or the NeoForge `DistExecutor` pattern.
- The mod MUST NOT hard-depend on any third-party mod. Optional integration (e.g., a
  Patchouli guidebook entry) MAY be added but MUST be guarded with a mod-loaded check and
  kept in a separate, clearly named class or package.
- The mod's item and block tags MUST include vanilla tag entries where applicable
  (e.g., `minecraft:logs`, `minecraft:planks`) so that vanilla recipes, fuel values, and
  world-generation references work without additional configuration.
- The `mods.toml` / `neoforge.mods.toml` descriptor MUST specify a precise NeoForge
  version range (`versionRange`) and MUST NOT use unbounded upper ranges in production
  releases.

## Development Workflow

- All features MUST follow the Specify workflow: `/speckit-specify` → `/speckit-clarify`
  → `/speckit-plan` → `/speckit-tasks` → `/speckit-implement`.
- Work MUST be done on feature branches (created via `/speckit-git-feature`); direct
  commits to `main` are PROHIBITED except for automated constitution/spec housekeeping
  commits produced by the Specify toolchain.
- Each completed task MUST result in a commit that builds successfully (Principle V).
  Commits MUST reference the task ID in the commit message (e.g., `feat(T012): add pale
  oak log block registration`).
- All data files (Principle III) MUST be generated or authored before the corresponding
  Java registration code is merged, ensuring the build never references a missing resource.

## Governance

- This constitution supersedes all informal conventions and prior agreements about project
  structure, scope, or tooling for this repository.
- Amendments MUST be proposed as a pull request that updates this file and increments the
  version per the semantic versioning policy below. The PR description MUST explain which
  principle is changing and why.
- **Versioning policy**:
  - **MAJOR** — backward-incompatible governance change: a principle is removed, its scope
    is materially narrowed, or a mandatory constraint is relaxed without replacement.
  - **MINOR** — a new principle or section is added, or existing guidance is materially
    expanded.
  - **PATCH** — clarifications, wording improvements, typo fixes, or non-semantic
    refinements that do not change intent.
- All pull requests MUST include a Constitution Check (in `plan.md`) verifying the
  proposed changes comply with all five core principles.
- Compliance review is expected at the start of each planning cycle (when
  `/speckit-plan` is run) and before any release tag is created.

**Version**: 1.0.0 | **Ratified**: 2026-05-24 | **Last Amended**: 2026-05-24
