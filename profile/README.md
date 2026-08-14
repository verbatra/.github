<p align="center">
  <img src="https://raw.githubusercontent.com/verbatra/.github/main/profile/assets/banner.webp" alt="verbatra: automated i18n translation for modern applications" />
</p>

<h1 align="center">verbatra</h1>

<p align="center">
  Automate i18n translation and keep your locale files in sync across languages, using OpenAI, Anthropic, Gemini, DeepL, or an openai-compatible local or self-hosted model. A result that would break a placeholder or an ICU message is withheld, not written.
</p>

<p align="center">
  <a href="https://www.npmjs.com/package/@verbatra/cli"><img src="https://img.shields.io/npm/v/@verbatra/cli?label=%40verbatra%2Fcli" alt="@verbatra/cli npm version" /></a>
  <a href="https://www.npmjs.com/package/@verbatra/sdk"><img src="https://img.shields.io/npm/v/@verbatra/sdk?label=%40verbatra%2Fsdk" alt="@verbatra/sdk npm version" /></a>
  <a href="https://www.npmjs.com/package/@verbatra/studio"><img src="https://img.shields.io/npm/v/@verbatra/studio?label=%40verbatra%2Fstudio" alt="@verbatra/studio npm version" /></a>
  <a href="https://github.com/marketplace/actions/verbatra"><img src="https://img.shields.io/github/v/release/verbatra/action?sort=semver&amp;label=marketplace&amp;color=blue" alt="GitHub Marketplace" /></a>
  <a href="https://github.com/verbatra/verbatra/blob/main/LICENSE"><img src="https://img.shields.io/badge/license-MIT-blue.svg" alt="License: MIT" /></a>
</p>

## What verbatra is

verbatra translates your application's locale files, and refuses to save a bad
translation. Every candidate value passes an integrity gate before it reaches
disk: a result that drops or alters a placeholder, breaks ICU structure,
collapses into runaway output, or comes back empty is withheld and reported
rather than written. A withheld value is not recorded in the lock file either,
so the previous translation stays intact and the key stays pending for the next
run. For the double-brace formats (i18next, ngx-translate, and YAML) the gate
also rejects a single-brace `{name}` token that the model invented and the
source never had.

The same gate guards provider output, an edit made by hand in Studio, and a
value read back from a translator's workbook, so a human-typed translation is
held to exactly the standard a machine-produced one is.

Around that gate, verbatra keeps runs small. You maintain one source locale by
hand; on every run verbatra diffs it against a committed lock file and sends
only the keys that are new or whose source text changed to the AI or
machine-translation provider you configure. Translations that are already
current are left untouched. Files round-trip in exact document key order, so
translated locale files diff cleanly.

verbatra is open source under the MIT license.

## What ships today

| | What it is |
| --- | --- |
| [`@verbatra/cli`](https://www.npmjs.com/package/@verbatra/cli) | The `verbatra` command for the terminal and CI. It scaffolds a project, translates once or keeps translating as you edit, reports locale state without writing or spending anything, hands pending strings to a human translator and reads them back, and serves the local dashboard. The [CLI reference](https://verbatra.kreitz-webdev.de/docs/cli) lists every subcommand and flag. |
| [`@verbatra/sdk`](https://www.npmjs.com/package/@verbatra/sdk) | The same engine as a programmatic API. verbatra is built SDK-first, so anything the CLI does you can also do in code. |
| [`@verbatra/studio`](https://www.npmjs.com/package/@verbatra/studio) | A local web dashboard over your project, served by `verbatra studio`. It binds to `127.0.0.1` only, and provider-spending actions exist only behind an explicit `--allow-spend` flag. |
| [`verbatra/action`](https://github.com/marketplace/actions/verbatra) | A composite GitHub Action on the GitHub Marketplace. Its `command` input runs `translate`, `check`, or `diff`; either read-only command gates a pull request on locale drift and needs no provider API key, so it also runs on a fork's pull request. Results arrive as annotations and a job summary. Consumed with `uses:`, not installed from npm. |

## Where the code lives

The engine (the CLI, the SDK, and Studio) is developed in the monorepo at
[github.com/verbatra/verbatra](https://github.com/verbatra/verbatra). That
is where issues about translation behavior, formats, providers, and the CLI
belong.

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

# 3. Provide the provider's API key (Gemini shown)
export GEMINI_API_KEY=your-key-here

# 4. Translate every target locale once
npx verbatra translate
```

Gemini has a real free tier, so it is the cheapest way to try verbatra. Pass
`anthropic`, `openai`, or `deepl` to `--provider` instead if you prefer one of
those. `verbatra check` and `verbatra diff` report locale state without writing
anything and without constructing a provider, so they need no API key at all:
`check` exits non-zero when any locale has missing or stale keys, and `diff`
exits non-zero when any locale has pending changes. That makes either one a
read-only CI gate, in the terminal or through the GitHub Action.

The full walkthrough is
[Your first translation](https://verbatra.kreitz-webdev.de/docs/your-first-translation).

## Formats and providers

Eight locale formats: JSON for i18next, vue-i18n, next-intl, and ngx-translate,
plus XLIFF, YAML, Flutter ARB, and Java/Spring properties
([Formats](https://verbatra.kreitz-webdev.de/docs/formats)).

Five providers behind one interface: Anthropic, OpenAI, Gemini, and
openai-compatible (a local or self-hosted server) as LLMs, plus DeepL as machine
translation ([Providers](https://verbatra.kreitz-webdev.de/docs/providers)).

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
- [GitHub Action](https://verbatra.kreitz-webdev.de/docs/github-action)

## Issues and security

Report bugs and feature requests as GitHub issues, in the repository that owns
the surface:

- Translation behavior, formats, providers, the CLI, the SDK, or Studio:
  [verbatra/verbatra/issues](https://github.com/verbatra/verbatra/issues)
- The GitHub Action's inputs, annotations, or job summary:
  [verbatra/action/issues](https://github.com/verbatra/action/issues)

Do not report a security vulnerability as a public issue. Use GitHub's private
vulnerability reporting on the affected repository:
[the engine's security policy](https://github.com/verbatra/verbatra/blob/main/SECURITY.md)
or
[the action's security policy](https://github.com/verbatra/action/blob/main/SECURITY.md).

## License

[MIT](https://github.com/verbatra/verbatra/blob/main/LICENSE) (c) Mario Kreitz
