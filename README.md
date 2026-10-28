# Remove AI Marks

An Agent Plugins 1.0 plugin for inspecting and cleaning supported content. It
includes a secure, local Docker Compose launcher for the compatible
[`watermarks-remover` v0.7.0](https://github.com/guillaumemeyer/watermarks-remover/releases/tag/v0.7.0)
HTTP service, so the skill is usable after a short setup instead of relying on
an unspecified server.

## Quick start: run the local service

Install Docker Desktop (or Docker Engine with the Compose plugin), then run
from the plugin checkout:

```bash
cp .env.example .env
docker compose up -d --wait --wait-timeout 120
curl -fsS http://127.0.0.1:8765/health
```

The service is reachable only from the local machine at
`http://127.0.0.1:8765`, which is also the skill's default
`WATERMARKS_SERVICE_URL`. Stop it with:

```bash
docker compose down
```

The compose file pins the upstream service to `v0.7.0`, uses a read-only
filesystem, a temporary `/tmp`, an unprivileged user, and an explicit
loopback-only port binding. It does not mount your project files into the
container: the skill submits only the selected file bytes to the local HTTP
service.

The upstream `v0.7.0` image is currently published for `linux/amd64`. The
launcher selects that platform automatically, so Docker Desktop on Apple Silicon
uses its built-in emulation. The first startup can therefore take longer than
on an Intel/AMD machine. The `--wait` flag above waits for the service health
check rather than returning as soon as the container process starts.

## Text: enable Layer B rewriting

The core image can deterministically remove invisible Unicode and file metadata
without a model. It cannot rewrite prose by itself. The bundled
`service-config/clean_strategy.json` enables a `paraphrase@0.8` Layer B
strategy; configure a rewrite backend in your untracked `.env` to use it.

For example, with a local Ollama server and a model you have already pulled:

```dotenv
WATERMARKS_REWRITE_BACKEND=ollama
WATERMARKS_REWRITE_MODEL=llama3.2
WATERMARKS_REWRITE_BASE_URL=http://host.docker.internal:11434
WATERMARKS_REWRITE_ALLOW_REMOTE=1
```

Then restart and wait for the service:

```bash
docker compose down
docker compose up -d --wait --wait-timeout 120
```

An OpenAI-compatible endpoint is also supported; use the commented variables
in `.env.example`. Never commit API keys. A remote endpoint requires explicit
`WATERMARKS_REWRITE_ALLOW_REMOTE=1` because text leaves the local service.

An AI-detector percentage is not evidence of a technical watermark and no
cleaning result can prove that text is human-written. The default image has no
vendor text detector or pixel-watermark backend: it reports those limitations
through `/capabilities`. Treat a successful result only as evidence that the
reported Unicode/metadata findings changed.

To require a bearer token, set a value for `WATERMARKS_SERVER_API_KEY` in
`.env` before starting the service and export the same value for the agent
client. Keep `.env` private; it is intentionally ignored by Git.

For a remote service, set `WATERMARKS_SERVICE_URL` to its HTTPS URL. Do not
send the API key over plain HTTP.

## What it provides

- Inspection-first workflows for invisible Unicode, provenance metadata, and
  supported content containers.
- A local service launcher based on the public upstream implementation.
- Guidance for supported text, image, document, and container formats.
- Explicit reporting of service capabilities and remaining limitations.

## Installation in Codex and compatible hosts

This repository is a portable Agent Plugins 1.0 package. Download its source
archive and load it using the local-plugin installation workflow supported by
your Codex or ChatGPT host. The package entry point is `plugin.json`; the
workflow is located at `skills/remove-ai-marks/SKILL.md`.

## Installation in Claude Code

Claude Code users can add this repository as a marketplace and install the
plugin by name:

```bash
claude plugin marketplace add abylkhaiyrov/remove-ai-marks
claude plugin install remove-ai-marks@remove-ai-marks-marketplace
```

The plugin exposes the skill as `/remove-ai-marks:remove-ai-marks`. To update
an installed copy after a new release, run:

```bash
claude plugin update remove-ai-marks@remove-ai-marks-marketplace
```

For a one-session test from a local checkout, run:

```bash
claude --plugin-dir ./remove-ai-marks
```

## Responsible use

Use the workflow only with content you own or are authorized to process. A
cleaning result does not establish that content was written by a human, and
some provenance and pixel-domain marks can remain detectable.

## License

MIT. See [LICENSE](LICENSE).

## Upstream attribution

The service image is provided by the independent upstream project
[guillaumemeyer/watermarks-remover](https://github.com/guillaumemeyer/watermarks-remover).
This repository owns the plugin packaging and launcher; it does not claim
authorship of that service. See [NOTICE.md](NOTICE.md) for the pinned version
and license notice.
