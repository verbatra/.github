<p align="center">
  <img src="https://raw.githubusercontent.com/verbatra/.github/main/profile/assets/banner.webp" alt="verbatra: incremental i18n translation automation" />
</p>

<h1 align="center">verbatra</h1>

<p align="center">
  Incremental i18n translation automation. You maintain one source locale by hand; on every run verbatra diffs it against a committed lock file and translates only the keys that changed since, through OpenAI, Anthropic, Gemini, DeepL, Google Cloud Translation, or an openai-compatible local or self-hosted model. A result that would break a placeholder or an ICU message is withheld, not written.
</p>

<p align="center">
  <a href="https://www.npmjs.com/package/@verbatra/cli"><img src="https://img.shields.io/npm/v/%40verbatra%2Fcli?label=%40verbatra%2Fcli&amp;color=7b1fa2&amp;labelColor=0B0B12" alt="@verbatra/cli npm version" /></a>
  <a href="https://www.npmjs.com/package/@verbatra/sdk"><img src="https://img.shields.io/npm/v/%40verbatra%2Fsdk?label=%40verbatra%2Fsdk&amp;color=7b1fa2&amp;labelColor=0B0B12" alt="@verbatra/sdk npm version" /></a>
  <a href="https://www.npmjs.com/package/@verbatra/studio"><img src="https://img.shields.io/npm/v/%40verbatra%2Fstudio?label=%40verbatra%2Fstudio&amp;color=7b1fa2&amp;labelColor=0B0B12" alt="@verbatra/studio npm version" /></a>
  <a href="https://www.npmjs.com/package/@verbatra/mcp"><img src="https://img.shields.io/npm/v/%40verbatra%2Fmcp?label=%40verbatra%2Fmcp&amp;color=7b1fa2&amp;labelColor=0B0B12" alt="@verbatra/mcp npm version" /></a>
  <a href="https://github.com/verbatra/verbatra/blob/main/LICENSE"><img src="https://img.shields.io/badge/license-MIT-blue.svg?color=7b1fa2&amp;labelColor=0B0B12" alt="License: MIT" /></a>
</p>

## What verbatra is

verbatra translates your application's locale files, and refuses to save a bad
translation. Every candidate value passes an integrity gate before it reaches
disk: a result that drops or alters a placeholder, breaks ICU structure,
collapses into runaway output, or comes back empty is withheld and reported
rather than written. A withheld value is not recorded in the lock file either,
so the previous translation stays intact and the key stays pending for the next
run.

The same gate guards provider output, an edit made by hand in Studio, an edit
made by an agent through the MCP server, and a value read back from a
translator's workbook, so a human-typed translation is held to exactly the
standard a machine-produced one is.

Around that gate, verbatra keeps runs small. Translations that are already
current are left untouched, and files round-trip in exact document key order, so
translated locale files diff cleanly.

verbatra is open source under the MIT license.

## What ships today

| | What it is |
| --- | --- |
| [`@verbatra/cli`](https://www.npmjs.com/package/@verbatra/cli) | The `verbatra` command for the terminal and CI, from scaffolding a project to translating, reporting, exporting for a human translator, and serving the dashboard ([CLI reference](https://verbatra.kreitz-webdev.de/docs/cli)). |
| [`@verbatra/sdk`](https://www.npmjs.com/package/@verbatra/sdk) | The same engine as a programmatic API, because verbatra is built SDK-first ([SDK reference](https://verbatra.kreitz-webdev.de/docs/sdk)). |
| [`@verbatra/studio`](https://www.npmjs.com/package/@verbatra/studio) | A local web dashboard over your project, bound to `127.0.0.1`, whose provider-spending actions exist only behind an explicit `--allow-spend` flag ([Studio](https://verbatra.kreitz-webdev.de/docs/cli/studio)). |
| [`@verbatra/mcp`](https://www.npmjs.com/package/@verbatra/mcp) | A stdio MCP server exposing translation status, the glossary, and gated editing as tools, so a coding agent can work the same project without a browser ([MCP server](https://verbatra.kreitz-webdev.de/docs/cli/mcp)). |
| [`verbatra/action`](https://github.com/marketplace/actions/verbatra) | A composite GitHub Action whose `command` input runs `translate`, `check`, or `diff`; the read-only commands need no API key, so they gate a fork's pull request too ([GitHub Action](https://verbatra.kreitz-webdev.de/docs/github-action)). |

## Where the code lives

The engine (the CLI, the SDK, Studio, and the MCP server) is developed in the
monorepo at [github.com/verbatra/verbatra](https://github.com/verbatra/verbatra).
That is where issues about translation behavior, formats, providers, the CLI,
the SDK, Studio, and the MCP tools belong.

This organization also hosts
[verbatra/action](https://github.com/verbatra/action), the composite GitHub
Action, which is published on the
[GitHub Marketplace](https://github.com/marketplace/actions/verbatra) and is
referenced as `verbatra/action@v1` or, for an immutable pin, by commit SHA. The
action's README carries the workflow examples. Issues about its inputs,
annotations, or job summary belong in that repository.

## Quick start

Needs Node.js `>=22.14.0`. verbatra installs as a development dependency, which
puts the `verbatra` binary in `node_modules/.bin` rather than on your PATH, so
the commands below call it through `npx`:

```bash
# 1. Install as a dev dependency
npm install --save-dev @verbatra/cli

# 2. Scaffold verbatra.config.ts and .env.example (choose your provider)
npx verbatra init --provider gemini

# 3. Provide the provider's API key. init created .env.example and gitignored
#    .env, so you can set it in .env, or export it (Gemini shown):
export GEMINI_API_KEY=your-key-here

# 4. Translate every target locale once
npx verbatra translate
```

Gemini has a real free tier, so it is the cheapest way to try verbatra. Pass
`anthropic`, `openai`, `deepl`, or `google-translate` to `--provider` instead if
you prefer one of those. `verbatra check` and `verbatra diff` report locale state
without writing anything and without constructing a provider, so they need no API
key at all: `check` exits non-zero when any locale has missing or stale keys, and
`diff` exits non-zero when any locale has pending changes. That makes either one
a read-only CI gate, in the terminal or through the GitHub Action.

The full walkthrough is
[Your first translation](https://verbatra.kreitz-webdev.de/docs/your-first-translation).

## Formats and providers

Fourteen locale formats: JSON for i18next, vue-i18n, next-intl, and
ngx-translate, plus XLIFF, YAML, Flutter ARB, Java/Spring properties, Apple
`.strings`/`.stringsdict`, Apple `.xcstrings`, Android `strings.xml`, gettext
`.po`/`.pot`, INI, and .NET `.resx`
([Formats](https://verbatra.kreitz-webdev.de/docs/formats)).

Six providers behind one interface: Anthropic, OpenAI, Gemini, and
openai-compatible (a local or self-hosted server) as LLMs, plus DeepL and Google
Cloud Translation as machine translation
([Providers](https://verbatra.kreitz-webdev.de/docs/providers)).

API keys are read only from environment variables, never from the config file
and never from a CLI argument.

For strings a machine should not translate,
[`verbatra export` and `verbatra import`](https://verbatra.kreitz-webdev.de/docs/manual-translation)
hand the pending strings to a human translator as an Excel workbook and read the
filled file back through the same safety checks.

## Documentation

[verbatra.kreitz-webdev.de](https://verbatra.kreitz-webdev.de/) is the canonical
reference.

- [Introduction](https://verbatra.kreitz-webdev.de/docs)
- [CLI reference](https://verbatra.kreitz-webdev.de/docs/cli)
- [Configuration](https://verbatra.kreitz-webdev.de/docs/config-file)
- [SDK reference](https://verbatra.kreitz-webdev.de/docs/sdk)
- [Verbatra Studio](https://verbatra.kreitz-webdev.de/docs/cli/studio)
- [MCP server](https://verbatra.kreitz-webdev.de/docs/cli/mcp)
- [GitHub Action](https://verbatra.kreitz-webdev.de/docs/github-action)

## Issues and security

Report bugs and feature requests as GitHub issues, in the repository that owns
the surface:

- Translation behavior, formats, providers, the CLI, the SDK, Studio, or the MCP
  server: [verbatra/verbatra/issues](https://github.com/verbatra/verbatra/issues)
- The GitHub Action's inputs, annotations, or job summary:
  [verbatra/action/issues](https://github.com/verbatra/action/issues)

Do not report a security vulnerability as a public issue. Use GitHub's private
vulnerability reporting on the affected repository:
[the engine's security policy](https://github.com/verbatra/verbatra/blob/main/SECURITY.md)
or
[the action's security policy](https://github.com/verbatra/action/blob/main/SECURITY.md).

## License

[MIT](https://github.com/verbatra/verbatra/blob/main/LICENSE) (c) Mario Kreitz
