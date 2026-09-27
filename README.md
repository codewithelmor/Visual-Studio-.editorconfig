# .editorconfig

A comprehensive `.editorconfig` file for .NET / C# projects, with every setting explained inline. Drop it in your solution root (next to the `.sln` file) and it will automatically apply to every project underneath it.

## What is EditorConfig?

[EditorConfig](https://editorconfig.org) is a simple, editor-agnostic way to define and maintain consistent coding styles across a team, regardless of which IDE or editor each person uses (Visual Studio, VS Code, Rider, etc.). Editors that support it read the closest `.editorconfig` file to whatever file you're editing and apply the matching rules automatically — no plugin configuration required on each person's machine.

For .NET specifically, `.editorconfig` does double duty:

1. **Formatting** — indentation, spacing, brace style, line endings (enforced by the IDE's auto-format / format-on-save).
2. **Code style analysis** — the Roslyn compiler and analyzers read `dotnet_*` / `csharp_*` style rules and surface violations as build warnings/suggestions (`IDE####` diagnostics), and can even fail the build if set to `error`.

## How EditorConfig resolves settings

- Editors search upward from the file being edited, through every parent directory, collecting `.editorconfig` files.
- The search stops once a file with `root = true` is found.
- Settings from files closer to the edited file take precedence over settings from files further away, so you can nest a more specific `.editorconfig` in a subfolder to override the top-level one.

## Structure of this file

| Section | Applies to | Covers |
|---|---|---|
| `[*]` | Every file | Charset, indentation, line endings, trailing whitespace, max line length |
| File-type overrides (`*.md`, `*.yml`, `*.json`, `*.xml`/project files, web assets, shell/batch scripts, `Makefile`) | Specific extensions | Sensible per-format tweaks (e.g. 2-space YAML/JSON, LF for shell scripts) |
| `[*.{cs,vb}]` | C# and VB files | Using-directive sorting, `this.`/`Me.` qualification, predefined types, parentheses, modifiers, expression-bodied members, pattern matching, null-checking, modern C# syntax preferences |
| Formatting rules | C# files | Brace style, indentation of blocks/`switch`/labels, spacing around operators/parentheses/commas, line-wrapping behavior |
| Naming conventions (`dotnet_naming_*`) | C# symbols | Interfaces prefixed `I`, PascalCase types/members, `_camelCase` private fields, `s_camelCase` static private fields, PascalCase constants, camelCase locals/parameters, `T`-prefixed generic type parameters |
| Analyzer severities | C# files | Template for overriding specific `CA####`/`IDE####` diagnostic severities, or whole analyzer categories |
| Generated code | `*.designer.cs`, `*.g.cs`, etc. | Excludes auto-generated files from style enforcement |

Every setting has an inline comment above it explaining:
- **What it controls**
- **The possible values it accepts**

## Severity suffixes

Most `dotnet_style_*` / `csharp_style_*` rules accept an optional `:severity` suffix:

| Severity | Effect |
|---|---|
| `none` | Rule is disabled entirely |
| `silent` | Applied only when using a code fix; no visible squiggle |
| `suggestion` | Shown as a suggestion (dots/message-level squiggle) |
| `warning` | Shown as a build warning |
| `error` | Shown as a build error — can fail CI if warnings-as-errors is on |

Example:
```ini
dotnet_style_readonly_field = true:suggestion
```

## Customizing this file

This file intentionally starts from Microsoft's documented defaults for most rules so it's a safe drop-in. You'll likely want to:

1. **Decide on `end_of_line`.** It's set to `crlf` (Windows-style) here — change to `lf` if your team is cross-platform / deploys to Linux and wants consistent line endings in git.
2. **Tighten severities for CI enforcement.** Rules are mostly `:silent` or `:suggestion` by default so they don't break existing builds. Bump the ones you care about to `:warning` or `:error`, and consider enabling [`.NET code style enforcement in build`](https://learn.microsoft.com/dotnet/fundamentals/code-analysis/overview) (`EnforceCodeStyleInBuild` in your `.csproj`) so these actually run on `dotnet build`, not just in the IDE.
3. **Adjust naming conventions** to match your team's actual conventions (e.g. if you don't prefix private fields with `_`).
4. **Uncomment / add specific diagnostic overrides** in the analyzer severities section as your team agrees on exceptions (e.g. disabling `CA2007` for ASP.NET Core projects that don't need `ConfigureAwait(false)`).

## Applying formatting in bulk

To reformat an entire existing codebase to match this file:

```bash
dotnet format
```

Or in Visual Studio: **Edit → Advanced → Format Document** (per file), or **Analyze → Code Cleanup → Run Code Cleanup (Profile 1)** across the whole solution after configuring the profile to include "Apply .editorconfig settings."

## References

- [EditorConfig.org](https://editorconfig.org)
- [.NET code-style rule options (Microsoft Learn)](https://learn.microsoft.com/dotnet/fundamentals/code-analysis/code-style-rule-options)
- [.NET naming rules](https://learn.microsoft.com/dotnet/fundamentals/code-analysis/style-rules/naming-rules)
- [Formatting rules](https://learn.microsoft.com/dotnet/fundamentals/code-analysis/style-rules/formatting-rules)
