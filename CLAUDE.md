# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Repository Purpose

This is a **localization sample-file repository** for the [Applanga](https://www.applanga.com/) translation management platform. It is not a software project — there is no build system, no executable code, no test suite, and no dependency manager. The repository serves two purposes:

1. Demonstrate the file formats Applanga can ingest and produce across different platforms/frameworks.
2. Act as the source-of-truth for translation strings synced bidirectionally with Applanga's cloud service via GitHub Actions.

## Applanga CLI Workflows

The `.applanga.json` file at the repo root drives all sync operations. It is read by the Applanga CLI when the workflows below run.

### Pushing source strings to Applanga

```bash
applanga push --force --fail-on-error
```

Triggered automatically on every push to `main` that touches `csv/de.csv`. Can also be triggered manually via the `applanga-push.yml` workflow dispatch, with an optional flag to push *translation* files instead of source files (`pushtarget`).

Required secrets: `APPLANGA_ACCESS_TOKEN`, `APPLANGA_API_HOST`.

### Pulling translated strings from Applanga

```bash
applanga pull --fail-on-error [--tag <tag>] [--language <lang>]
```

Manual-only (`workflow_dispatch`) via `applanga-pull.yml`. The workflow creates a PR into the target branch with the pulled translations. Inputs available at dispatch time: `tags`, `languages`, `pr_title`, `commit_message`, `target_branch`, `source_branch`.

### .applanga.json configuration

| Key | Value |
|-----|-------|
| `base_language` | `en` |
| Push source | `./csv/de.csv` (treated as English source, keyed by `app:language.csv` tag) |
| Pull target pattern | `./csv/<language>.csv` (all non-English languages, excludes `en`) |

The column layout for CSV files: column 0 = translation key, column 1 = translation value. Header row is excluded.

## Localization File Formats

Each subdirectory holds sample files for one format. The table below maps directory → platform/framework:

| Directory | Format | Platform/Framework |
|-----------|--------|--------------------|
| `android_xml/` | Android XML strings | Android |
| `angular_translate_json/` | Angular Translate JSON | Angular.js |
| `chrome_i18n_json/` | Chrome Extension manifest JSON | Chrome Extensions |
| `csv/` | CSV | Generic (primary exchange format) |
| `ember_i18n_json_module/` | Ember i18n JSON module | Ember.js |
| `excel/` | XLSX | Spreadsheet-based workflows |
| `flutter_arb/` | ARB (Application Resource Bundle) | Flutter / Dart |
| `gettext_po/` | PO (Portable Object) | GNU gettext |
| `gettext_pot/` | POT (Portable Object Template) | GNU gettext |
| `go_i18n_json/` | go-i18n JSON | Go |
| `i18next_json/` | i18next JSON | JavaScript (i18next) |
| `ini_file/` | INI | Generic configuration |
| `ios_strings/` | `.strings` | iOS / macOS |
| `ios_stringsdict/` | `.stringsdict` (plist XML) | iOS plurals |
| `java_properties/` | Java `.properties` | Java |
| `microsoft_resw/` | RESW (XML) | .NET / UWP |
| `node_2_json/` | Node.js JSON module | Node.js |
| `xliff/` | XLIFF 2.0 (XML) | Standard i18n exchange (nested path) |

Languages present across the repo: English (`en`), German (`de`), French (`fr`), Korean (`ko`), Danish (`da`), Mongolian (`mn`).

## Key Conventions

- `csv/de.csv` is the **canonical source file**. Changes to it trigger the push workflow.
- When adding a new language, add its CSV file as `csv/<lang-code>.csv` and ensure the pull workflow's `columnDescription` pattern matches.
- The `xliff/` directory uses a deeply nested path structure — preserve it when modifying XLIFF samples.
- Do not alter `.applanga.json` without understanding the push/pull impact; the column indices must match the actual CSV structure.
