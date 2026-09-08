---
name: compose-multiplatform-updater
description: Upgrade Compose Multiplatform in Kotlin Multiplatform projects using the exact JetBrains release notes for the requested version. Use this skill whenever the user asks to update Compose Multiplatform, the org.jetbrains.compose Gradle plugin, CMP libraries, or compatible Compose-related dependencies, even when the request only names a target version or says to update the Compose stack.
compatibility: Requires a Kotlin Multiplatform project, network access to the JetBrains Compose Multiplatform GitHub release notes, and a Gradle-based codebase.
metadata:
  author: AppOutlet
  version: "1.0"
---

# Compose Multiplatform Updater

Update a Compose Multiplatform dependency together with the compatible versions explicitly published for that release. Compose Multiplatform releases can pin a set of Kotlin Multiplatform library coordinates to versions that differ from the upstream Jetpack versions, so do not infer compatibility from the AndroidX release train or from the latest available version.

## Workflow

1. Parse the requested target version. Accept `1.12.0` and `v1.12.0` forms, but do not guess a version when the user did not provide one. Ask for it.
2. Inspect repository guidance before editing. Read relevant `AGENTS.md`, `CONTRIBUTING.md`, and build documentation, then identify the project module(s) that use Compose.
3. Fetch the exact JetBrains release notes for the target version:
   - Prefer `https://github.com/JetBrains/compose-multiplatform/releases/tag/v<version>`.
   - If the page is unavailable or incomplete, use the GitHub release API for `JetBrains/compose-multiplatform` and record the source URL.
   - Never substitute the latest release, a similarly named release, a blog post, or an upstream AndroidX release page.
4. Read the complete release notes, especially `Migration Notes`, `Components`, and `Libraries`. Also look for explicit requirements involving Kotlin, Gradle, Android Gradle Plugin, Compose compiler, Skiko, or other build plugins. Treat an explicit requirement as a compatibility constraint, not as permission to upgrade every related tool.
5. Inventory the current project before making changes. Search all relevant Gradle settings and build files, including:
   - `settings.gradle(.kts)`, root and module `build.gradle(.kts)` files
   - `gradle/libs.versions.toml`
   - `buildSrc/`, convention plugins, and `gradle.properties`
   - dependency-management or lock files when present
   Find the Compose plugin, Compose Multiplatform library coordinates, version aliases, and properties used to define their versions. Follow aliases to their actual values.
6. Build a compatibility table before editing. For each Compose-related dependency currently present, record its file or alias, current version, exact release-note coordinate, compatible target version, and source URL. Include dependencies only when the release notes provide an applicable mapping. Distinguish these carefully:
   - `org.jetbrains.compose:*` or other Compose Multiplatform coordinates listed in the table are the versions to use for the project.
   - The linked `androidx.compose` or Jetpack version is reference information and must not replace the Compose Multiplatform version.
   - Do not add a library that the project does not already use.
   - Do not change unrelated dependencies merely because newer versions exist.
7. Apply the smallest edits necessary. Prefer changing a shared version catalog entry or property over duplicating versions. Preserve aliases, dependency scopes, formatting, comments, repository structure, and platform-specific source sets. Update the `org.jetbrains.compose` plugin to the requested version and update present compatible libraries from the mapping. Update Kotlin, Compose compiler, AGP, Gradle, or other tooling only when the target release notes explicitly require a compatible version and the current project value conflicts with it; otherwise leave them unchanged and call out the constraint.
8. Inspect the diff immediately. Confirm that every changed version is justified by the compatibility table and that no unrelated dependency or generated file changed. If a present dependency has no unambiguous mapping, do not guess: leave it unchanged and report it as requiring a decision.
9. Validate according to the repository's instructions. Start with a targeted Gradle configuration or compile/test task for the affected module and platform when practical. Use the project's prescribed checks; do not run expensive all-platform suites or lint tasks unless requested or required by the local guidance. If network or toolchain limitations prevent validation, report that clearly rather than claiming success.

## Release-note interpretation

The `Libraries` section commonly has rows such as a library group, a wildcard coordinate, a Compose Multiplatform version, and a separate `Based on Jetpack` version. Use the Compose Multiplatform version in the project's `org.jetbrains.*` coordinate. The Jetpack link is useful for context but is not the value to write into a CMP dependency.

When a row uses a wildcard (`runtime*`, `ui*`, `navigation-*`), match it to the project's actual artifact and preserve the artifact suffix. When multiple rows could match, prefer the exact group and artifact match; if ambiguity remains, stop before editing that dependency.

The Compose plugin version and library versions may not all be identical. Do not normalize them to the target version unless the release notes say so. Similarly, the Kotlin Compose compiler plugin is coupled primarily to Kotlin in many projects and is not automatically the same as `org.jetbrains.compose`; change it only when the release notes or project build logic explicitly couples it.

## Report

Finish with:

1. The target Compose Multiplatform version and the exact release-notes URL.
2. A concise table of changed coordinates with old and new versions.
3. Any compatible dependencies found in the release notes but not used by the project.
4. Any present dependencies that were not changed, with the reason (not listed, already compatible, ambiguous, or explicitly left outside scope).
5. Validation commands run and their outcomes.

Do not claim that the upgrade is complete if the exact release notes could not be fetched, the dependency mapping is ambiguous, or the relevant validation failed.
