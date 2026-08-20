<p align="center">
  <img src="assets/atlas-hero.png" width="220" alt="Atlas — the Titan carrying the knowledge graph">
</p>

<h1 align="center">Atlas</h1>

<p align="center"><b>STATE &bull; KNOWLEDGE &bull; GRAPH</b></p>

<p align="center"><b>Persistent state, knowledge and graph infrastructure for the Titans ecosystem.</b><br>
One local-first executable that stores what you and your agents know — and answers with sources.</p>

<p align="center">
  <a href="https://github.com/titanslmachado/Atlas/releases/latest"><img alt="Release" src="https://img.shields.io/badge/release-0.2.0-1f6feb"></a>
  <img alt="Platforms" src="https://img.shields.io/badge/platforms-Windows%20x64%20%7C%20Linux%20x64-3fb950">
  <img alt="Install" src="https://img.shields.io/badge/install-one%20line-3fb950">
  <img alt="Signed" src="https://img.shields.io/badge/releases-Ed25519%20signed-8b5cf6">
  <a href="./LICENSE.md"><img alt="License" src="https://img.shields.io/badge/license-EULA-lightgrey"></a>
  <img alt="Free tier" src="https://img.shields.io/badge/free%20tier-yes-3fb950">
</p>

---

## Install

One line. No Git, no Rust, no admin rights. The installer pins the release
public key and the SHA-256 of the bootstrap; the bootstrap verifies the signed
catalog and every archive (hash + Ed25519) before anything runs.

**Windows x86_64** — PowerShell:

```powershell
irm https://github.com/titanslmachado/Atlas/releases/latest/download/Install-Atlas.ps1 | iex
```

**Linux x86_64**:

```bash
curl -fsSL https://github.com/titanslmachado/Atlas/releases/latest/download/install-atlas.sh | sh
```

Already have any Titan? Then it is just:

```bash
titans install atlas
```

Prefer a manual download? Every release ships signed archives per platform on
the [Releases](https://github.com/titanslmachado/Atlas/releases) page
(`bin/atlas` inside; SHA-256 and signature in `signed-distribution-catalog.json`).
macOS and ARM64 are not part of this release.

## What Atlas is

Atlas keeps **knowledge packages, a typed graph, work sessions, per-project SQL
databases, analytics and blobs** in a single transactional store on your
machine. Search is hybrid — full text, vectors, graph and evidence — and every
write lands in a hash-chained audit log, so *"what do we know, and how do we
know it"* always has a verifiable answer.

Atlas is the authoritative persistence backbone of Titans: a recoverable,
versioned source of truth that lets Titans and agents share state through
canonical references instead of isolated databases or duplicated context.
Nothing is sent anywhere — Atlas binds to loopback, has no telemetry, and works
offline (the semantic model downloads on first use; lexical search works before
it arrives).

**Core capabilities:** knowledge storage &middot; full-text search &middot; vector retrieval &middot; knowledge graphs &middot; evidence and context management &middot; sessions and checkpoints &middot; event sourcing &middot; caching &middot; versioning &middot; recovery

## The first 60 seconds

```bash
atlas status
```

```bash
atlas call atlas atlas_add '{"package":{"schema_version":"atlas/1.0","package_id":"note-1","tenant":"acme","project":"alpha","title":"Kickoff","markdown":"We chose Postgres for the ledger because of range partitioning."}}'
```

```bash
atlas call atlas atlas_search '{"tenant":"acme","project":"alpha","query":"why postgres"}'
```

The result carries the matching package, the score breakdown per signal and
the evidence behind it — not only a rank. Prefer a window? Run `atlas` with no
arguments (or `atlas config-ui`) to open the local configurator in your browser.

## Agents do not need a port

Atlas speaks **MCP over stdio**. The installer configures one shared MCP server
named `titans` in the AI clients present on the machine (Claude Code, Claude
Desktop, VS Code, Codex, Antigravity); every installed Titan appears there
without adding another server entry.

```bash
titans mcp configure --client vscode --yes
```

REST (`127.0.0.1:7741`, OpenAPI at `/openapi.json`, offline API browser at
`/docs`) and gRPC (`127.0.0.1:7742`) exist for programs that want them.

## What Atlas stores

Everything is scoped by `(tenant, project)`, so many people, projects and
agents share one store without stepping on each other.

| Domain | What it is | Start with |
|---|---|---|
| Knowledge packages | Documents with tags, sources and evidence | `atlas_add`, `atlas_search`, `atlas_context` |
| Typed graph | Entities and relations in layers | `atlas_graph_neighbors`, `atlas_graph_subgraph`, `atlas_graph_export_rdf` |
| Work state | Durable contexts, sessions, events, checkpoints and handoffs | `atlas_work_contexts`, `atlas_work_sessions`, `atlas_work_events` |
| SQL | A real SQLite database per project, quota-bounded | `atlas_sql_create`, `atlas_sql_execute`, `atlas_sql_query` |
| Analytics | DuckDB aggregation over projections, with sandboxed exports | `atlas_analytics_query`, `atlas_analytics_export` |
| Blobs | Content-addressed storage, chunked uploads, ranged reads | `atlas_blob_upload`, `atlas_blob_download` |
| Audit | A hash chain over every mutation | `atlas_audit_verify` |

Ask `atlas_projects_list` first (REST: `GET /atlas/projects`). 109 operations
are exposed identically over MCP, REST, gRPC and the Titans Fast Contract.

## Updates

Two independently signed channels — a public discovery catalog and a
distribution catalog — drive updates. Nothing changes without explicit
confirmation, and a version can be pinned.

```bash
titans versions check
```

## Free and paid

This release is **free**. Capabilities and versions that become paid are
published in the signed public catalog; rights you acquire for a version stay
with that version (see the [EULA](./LICENSE.md), §1.3).

## Security

Report vulnerabilities privately — see [SECURITY.md](./SECURITY.md).

## License

Proprietary. Use is governed by the [Titans End User License Agreement](./LICENSE.md).
Third-party components are listed in the SBOM inside every release archive.
This repository hosts the official signed releases; the source code is not
published.
