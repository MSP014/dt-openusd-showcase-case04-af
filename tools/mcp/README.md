# Omniverse MCP Helpers

Local development/reference clients for Case 04. They query NVIDIA
Omniverse MCP services for Kit, OpenUSD and OmniUI information; they are not
application runtime dependencies.

## Layout

| File | Purpose |
| --- | --- |
| `mcp_http_client.py` | Shared standard-library HTTP/JSON-RPC transport. |
| `kit_mcp_client.py` | Kit templates, extensions, settings and APIs. |
| `usd_mcp_client.py` | OpenUSD modules, classes, methods and examples. |
| `omni_ui_mcp_client.py` | OmniUI classes, layouts, styling and examples. |

The command interface and transport behaviour are consistent across the
Showreel projects. Each repository owns its local copy and project client
identity; another case's source tree is not an execution dependency.

## Requirements and endpoints

Use the project's `case04-env` Conda environment (Python 3.11.x baseline).
The clients use only the Python standard library. Check the interpreter first:

```powershell
conda run -n case04-env python -c "import sys; print(sys.executable)"
```

The services are shared local development infrastructure, configured outside
the repository:

| Service | Default URL | Process environment override |
| --- | --- | --- |
| Kit | `http://localhost:9902/mcp` | `KIT_MCP_URL` |
| USD Code | `http://localhost:9903/mcp` | `USD_MCP_URL` |
| OmniUI | `http://localhost:9901/mcp` | `OMNI_UI_MCP_URL` |

An explicit `--url` takes precedence over the process environment, followed by
the default above. Put `--url` before the subcommand. The clients do not load
`.env` automatically. Use the configured local endpoints; do not substitute a
remote service without approval or embed credentials in URLs.

## Usage and checks

Run commands from this repository's root:

```powershell
conda run -n case04-env python tools/mcp/kit_mcp_client.py --help
conda run -n case04-env python tools/mcp/kit_mcp_client.py list-tools
conda run -n case04-env python tools/mcp/usd_mcp_client.py list-tools
conda run -n case04-env python tools/mcp/omni_ui_mcp_client.py list-tools
```

Read-only reference queries:

```powershell
conda run -n case04-env python tools/mcp/kit_mcp_client.py search-knowledge "Kit extension lifecycle"
conda run -n case04-env python tools/mcp/usd_mcp_client.py class-detail "UsdStage"
conda run -n case04-env python tools/mcp/omni_ui_mcp_client.py class-detail "Button"
```

An endpoint override uses this form:

```powershell
conda run -n case04-env python tools/mcp/kit_mcp_client.py --url http://localhost:9902/mcp list-tools
```

`--help` checks local CLI availability. A successful `list-tools` checks the
MCP handshake and catalogue; an actual reference query additionally checks its
indexed content. A connection failure means that endpoint is unavailable, not
that a query was verified. Check service readiness and configuration before
changing project code.

## Service lifecycle

Docker Desktop and a separately configured local checkout of
`NVIDIA-Omniverse/kit-usd-agents` are required for the Docker-backed services.
`KIT_USD_AGENTS_ROOT` is an optional operator convenience for locating that
checkout; the clients themselves do not read it. Check that checkout's current
instructions, Compose file and local configuration before starting services.

When service startup is part of the task, the existing NGC Compose setup uses:

```powershell
Push-Location "$env:KIT_USD_AGENTS_ROOT\source\mcp"
docker compose --env-file .env -f docker-compose.ngc.yaml up -d usd-code-mcp kit-mcp omni-ui-mcp
Pop-Location
```

The matching stop operation is:

```powershell
Push-Location "$env:KIT_USD_AGENTS_ROOT\source\mcp"
docker compose --env-file .env -f docker-compose.ngc.yaml stop usd-code-mcp kit-mcp omni-ui-mcp
Pop-Location
```

These commands affect shared services and may need images or external access.
Adding the clients does not start services or configure server credentials.

## Houdini boundary

Live Houdini scene access uses the separately connected FX Houdini MCP bridge.
These HTTP clients do not load HIP files, execute HOM, or replace that bridge.
Reference answers from Kit/USD/OmniUI are not proof of Houdini node behaviour or
an implemented specialised solver/API integration.

## Repository boundary

Keep these small clients and this README public. Keep credentials, `.env`
files, upstream checkouts, Docker state, build output and generated indexes
outside versioned project content. Do not include private workflow instructions
or personal absolute filesystem paths in public documentation.
