# Brode.io — image to embroidery & vector MCP server

**Brode.io** converts any image into machine embroidery files (**DST, PES, JEF, VP3, EXP, U01, XXX**) or vector graphics (**SVG, PDF, EPS, DXF**).

Remote MCP server (Streamable HTTP, MCP 2025-06-18), hosted at **`https://brode.io/api/mcp`** — same engine and same free account as the [brode.io](https://brode.io) web app (free tier: 5 vectorizations + 2 embroideries per month).

## Tools

| Tool | What it does |
|---|---|
| `brode_convert` | Convert an uploaded image (`input_key`) to SVG/PDF/EPS/DXF or DST/PES/JEF/VP3/EXP/U01/XXX. Five rendering presets, advanced options, waits up to 30 s for completion. |
| `brode_presets` | Guidance: rendering presets (fidele, equilibre, epure, graphiste, minimaliste), output formats and common options. |
| `brode_job_status` | Poll a conversion (queued → processing → completed/failed) with progress stage and result metadata (colors, stitches, dimensions). |
| `brode_download` | Returns an **absolute download URL** for the result file, preview PNG, thread color chart PDF or stitch trace JSON — fetch it with your API key as Bearer. |

## Get an API key (free)

1. Create a free account at [brode.io/register](https://brode.io/register)
2. Open [Settings → API keys](https://brode.io/app/settings#api-keys) and create a key (`brode_…`)

## Configuration

### Claude Desktop / Cursor — `mcp.json`

```json
{
  "mcpServers": {
    "brode": {
      "type": "http",
      "url": "https://brode.io/api/mcp",
      "headers": { "Authorization": "Bearer brode_YOUR_KEY" }
    }
  }
}
```

### Claude Code

```bash
claude mcp add --transport http brode https://brode.io/api/mcp \
  --header "Authorization: Bearer brode_YOUR_KEY"
```

### ZCode — `~/.zcode/cli/config.json`

```json
{
  "mcp": {
    "servers": {
      "brode": {
        "type": "http",
        "url": "https://brode.io/api/mcp",
        "headers": { "Authorization": "Bearer brode_YOUR_KEY" }
      }
    }
  }
}
```

## Usage pattern (important)

Tool arguments are model-generated — **never inline image bytes**. Upload the file once with curl, then convert by reference:

```bash
curl -sS -H "Authorization: Bearer brode_YOUR_KEY" \
     -F "file=@image.png" \
     https://brode.io/api/mcp/upload
# → {"input_key":"uploads/<uuid>/source.png","ext":"png",...}
```

Then call `brode_convert`:

```json
{ "input_key": "uploads/<uuid>/source.png", "output_format": "dst", "wait_seconds": 30 }
```

And fetch the result with the URL returned by `brode_download`:

```bash
curl -sS -H "Authorization: Bearer brode_YOUR_KEY" -o design.dst "<url>"
```

Embroidery files carry the design name as their internal machine label (e.g. `LA:` in DST headers), taken from the uploaded filename.

## Quotas & pricing

Conversions use your [brode.io](https://brode.io) plan quota — free tier: 5 vectorizations + 2 embroideries per month; paid plans from €9.90/month ([pricing](https://brode.io/pricing)).

## Links

- Web app: <https://brode.io>
- Full MCP documentation: <https://brode.io/tools/mcp>
- Stitch simulator (free tool): <https://brode.io/tools/stitch-simulator>
- Support: [support@brode.io](mailto:support@brode.io)

---

This repository is the public documentation of the hosted Brode.io MCP server. The conversion engine itself is closed-source and runs on brode.io infrastructure.
