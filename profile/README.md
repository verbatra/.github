<p align="center">
  <img src="https://raw.githubusercontent.com/verbatra/action/main/.github/assets/banner.webp" alt="verbatra: automated i18n translation for modern applications" />
</p>

<h1 align="center">verbatra</h1>

<p align="center">
  Automate i18n translation and keep your locale files in sync across languages, using OpenAI, Anthropic, Gemini, DeepL, or an openai-compatible local or self-hosted model.
</p>

<p align="center">
  <a href="https://www.npmjs.com/package/@verbatra/cli"><img src="https://img.shields.io/npm/v/@verbatra/cli?label=%40verbatra%2Fcli" alt="@verbatra/cli npm version" /></a>
  <a href="https://www.npmjs.com/package/@verbatra/sdk"><img src="https://img.shields.io/npm/v/@verbatra/sdk?label=%40verbatra%2Fsdk" alt="@verbatra/sdk npm version" /></a>
  <a href="https://www.npmjs.com/package/@verbatra/studio"><img src="https://img.shields.io/npm/v/@verbatra/studio?label=%40verbatra%2Fstudio" alt="@verbatra/studio npm version" /></a>
  <a href="https://github.com/mariokreitz/verbatra/blob/main/LICENSE"><img src="https://img.shields.io/badge/license-MIT-blue.svg" alt="License: MIT" /></a>
</p>

## What verbatra is

verbatra translates your application's locale files for you. You maintain one
source locale by hand; on every run verbatra diffs it against a committed lock
file and sends only the keys that are new or whose source text changed to the AI
or machine-translation provider you configure. Translations that are already
current are left untouched.

Every candidate translation passes an integrity check before it is written. A
result that drops or alters a placeholder, or breaks ICU structure, is withheld
and reported rather than saved. Files round-trip in exact document key order, so
translated locale files diff cleanly.

verbatra is open source under the MIT license.

## What ships today

| | What it is |
| --- | --- |
| [`@verbatra/cli`](https://www.npmjs.com/package/@verbatra/cli) | The `verbatra` command for the terminal and CI. Eight subcommands: `init`, `translate`, `watch`, `check`, `diff`, `export`, `import`, `studio`. |
| [`@verbatra/sdk`](https://www.npmjs.com/package/@verbatra/sdk) | The same engine as a programmatic API. verbatra is built SDK-first, so anything the CLI does you can also do in code. |
| [`@verbatra/studio`](https://www.npmjs.com/package/@verbatra/studio) | A local web dashboard over your project, served by `verbatra studio`. It binds to `127.0.0.1` only, and provider-spending actions exist only behind an explicit `--allow-spend` flag. |
| [`verbatra/action`](https://github.com/verbatra/action) | A composite GitHub Action that runs `verbatra translate --json` in CI, turns failures into annotations, and writes a job summary. Consumed with `uses:`, not installed from npm. |

## Where the code lives

The engine (the CLI, the SDK, and Studio) is developed in the monorepo at
[github.com/mariokreitz/verbatra](https://github.com/mariokreitz/verbatra). That
is where issues about translation behavior, formats, providers, and the CLI
belong.

This organization currently hosts one repository,
[verbatra/action](https://github.com/verbatra/action), the composite GitHub
Action. Issues about the action's inputs, annotations, or job summary belong
there.

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
anything and exit non-zero when a locale is missing or stale, which makes them a
read-only CI gate.

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
  [mariokreitz/verbatra/issues](https://github.com/mariokreitz/verbatra/issues)
- The GitHub Action's inputs, annotations, or job summary:
  [verbatra/action/issues](https://github.com/verbatra/action/issues)

Do not report a security vulnerability as a public issue. Use GitHub's private
vulnerability reporting on the affected repository:
[the engine's security policy](https://github.com/mariokreitz/verbatra/blob/main/SECURITY.md)
or
[the action's security policy](https://github.com/verbatra/action/blob/main/SECURITY.md).

## License

[MIT](https://github.com/mariokreitz/verbatra/blob/main/LICENSE) (c) Mario Kreitz
