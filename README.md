# Remove AI Marks

An Agent Plugins 1.0 skills-only plugin for inspecting and cleaning supported
content through a separately configured watermark-cleaning service.

## What it provides

- Inspection-first workflows for invisible Unicode, provenance metadata, and
  supported content containers.
- Guidance for supported text, image, document, and container formats.
- Explicit reporting of service capabilities and remaining limitations.

## Requirements

This repository does not include, deploy, or host the cleaning service. Before
using the plugin, configure a service endpoint through `WATERMARKS_SERVICE_URL`.
The skill defaults to `http://127.0.0.1:8765` and supports an optional
`WATERMARKS_SERVER_API_KEY` environment variable for authenticated services.

Use an HTTPS endpoint whenever the service runs remotely so credentials are not
sent in clear text.

## Installation

This repository is a portable Agent Plugins 1.0 package. Download its source
archive and load it using the local-plugin installation workflow supported by
your Codex or ChatGPT host. The package entry point is `plugin.json`; the
workflow is located at `skills/remove-ai-marks/SKILL.md`.

## Responsible use

Use the workflow only with content you own or are authorized to process. A
cleaning result does not establish that content was written by a human, and
some provenance and pixel-domain marks can remain detectable.

## License

MIT. See [LICENSE](LICENSE).
