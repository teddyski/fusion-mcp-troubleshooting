# Fusion 360 MCP Bot — Troubleshooting Reference

Field notes from debugging AI-assisted parametric CAD using **Claude + the Fusion 360 MCP integration**.

Documents the failure modes the bot hit, the prompting patterns that actually worked, and the geometry conventions required to avoid silent no-op cuts. Preserved as a portfolio artifact of real-world AI-assisted CAD debugging.

## View the reference

View it hosted via GitHub Pages:

> **[https://teddyski.github.io/fusion-mcp-troubleshooting/](https://teddyski.github.io/fusion-mcp-troubleshooting/)**

Or open [`index.html`](./index.html) locally in a browser.

## What's inside

A single self-contained HTML page covering:

- **Session Start Checklist** — the "radial math firewall" block to paste at the top of every MCP session
- **Why radial math fails** — root-cause breakdown of the hallucination pattern
- **Working MCP prompt patterns** — templates that cut all 3 bayonet lock channels successfully
- **Entry slot extrude patterns** — when to use extrude cuts vs. revolve cuts
- **Python vs. MCP decision table** — which tool handles which operation reliably
- **Multi-body file rules** — what breaks and what works across multiple bodies
- **Diagnostic workflow** — ordered debugging steps when a cut removes 0 cm³
- **Units & coordinate reference** — cm vs. mm gotchas in the Fusion API
- **Case study: bayonet ring** — confirmed dimensions, pre-computed angle tables, and build state

## Key findings (TL;DR)

| What works | What fails |
|---|---|
| Pre-computed XY coordinates passed as raw numbers | Asking the MCP bot to do any radial math — infinite solution space |
| Explicit body targeting by name | Letting Fusion auto-pick the target body |
| MCP prompts for revolves and construction planes | `constructionPlanes.setByAngle()` in Python |
| Python for diagnostics, extrude cuts, volume checks | `circularPatternFeatures` on multi-body files |
| 0.1 cm outer-radius buffer on every cut | `participantBodies` on revolve inputs (silent 0 cm³) |

## Context

The reference was written while building a parametric multi-body assembly with radial / bayonet-style cut features. The core insight: LLMs hallucinate on 3D rotational geometry not because the math is hard, but because natural-language descriptions of radial geometry have too many valid interpretations (inner/outer radius, with/without buffer, measured from where). The fix is to close off that ambiguity by pre-computing every radius value before prompting.

## License

[MIT](./LICENSE) — use freely with attribution.