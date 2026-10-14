# JINA

![license](https://img.shields.io/badge/license-license-blue) ![offline-first](https://img.shields.io/badge/offline--first-air--gap-green) ![audit](https://img.shields.io/badge/audit-SHA3--256-orange) ![category](https://img.shields.io/badge/category-artist_tools-lightgrey)

> Anticloud-hardened packaging of the upstream project `JINA` in category **ARTIST TOOLS**. Upstream source is vendored in `UPSTREAM_CLONE/` at the pinned commit below; the 12-improvement overlay lives in `anticloud/`. Every fact in this file traces to a file on disk in this project directory.

**Category:** ARTIST TOOLS · **Upstream:** https://github.com/jina-ai/MCP · **Upstream pin:** `5d6eb191a75d8e67b6e01ce427f0cc5c05c800aa` · **Vendor:** Anticloud FZ LLE

---

## What This Project Does

# Jina AI Remote MCP Server

A remote Model Context Protocol (MCP) server for the Jina Reader, Search, Embeddings and Reranker APIs:

## Usage

> [!WARNING]
> Some clients do not support env variable, so you may need to replace `${JINA_API_KEY}` below to a hardcoded real API key `jina_xxx`.

For client that supports remote MCP server:
```json
{
  "mcpServers": {
    "jina-mcp-server": {
      "url": "https://mcp.jina.ai/v1",
      "headers": {
        "Authorization": "Bearer ${JINA_API_KEY}" // optional
      }
    }
  }
}
```

For Claude Code:

```bash
claude mcp add -s user --transport http jina https://mcp.jina.ai/v1 \
  --header "Authorization: Bearer ${JINA_API_KEY}"
```

For OpenAI Codex: find `~/.codex/config.toml` and add the following:

```toml
[mcp_servers.jina-mcp-server]
command = "npx"
args = [
    "-y",
    "mcp-remote",
    "https://mcp.jina.ai/v1",
    "--header",
    "Authorization: Bearer ${JINA_API_KEY}"]
```

For client that does not support remote MCP server yet, you need [`mcp-remote`](https://www.npmjs.com/package/mcp-remote) a local proxy to connect to the remote MCP server.

```json
{
  "mcpServers": {
    "jina-mcp-server": {
      "command": "npx",
      "args": [
        "mcp-remote",
        "https://mcp.jina.ai/v1",
        "--header",
        "Authorization: Bearer ${JINA_API_KEY}"
      ]
    }
  }
}
```
## Available tools

| Tool | Description | Is Jina API Key Required? |
|-----------|-------------|----------------------|
| `primer` | Get current contextual information for localized, time-aware responses | No |
| `read_url` | Read a web page or PDF as markdown. Pass `question` for passages instead of the full body via [Reader API](https://jina.ai/reader) | Optional* |
| `capture_screenshot_url` | Capture a screenshot of a web page via [Reader API](https://jina.ai/reader) | Optional* |
| `guess_datetime_url` | Guess a page's publish or last-update datetime, with a confidence score | No |
| `search_web` | Search the web. Returns titles, URLs and engine snippets via [Reader API](https://jina.ai/reader) | Yes |
| `search_arxiv` | Search academic papers and preprints on arXiv project via [Reader API](https://jina.ai/reader) | Yes |
| `search_ssrn` | Search academic papers on SSRN (Social Science Research Network) via [Reader API](https://jina.ai/reader) | Yes |
| `search_images` | Search the web for images via [Reader API](https://jina.ai/reader) | Yes |
| `search_jina_blog` | Search Jina AI news and blog posts at [jina.ai/news](https://jina.ai/news) | No |
| `sort_by_relevance` | Rerank documents by relevance to a query via [Reranker API](https://jina.ai/reranker) | Yes |
| `deduplicate_strings` | Get top-k semantically unique strings via [Embeddings API](https://jina.ai/embeddings) and [submodular optimization](https://jina.ai/news/submodular-optimization-for-diverse-query-generation-in-deepresearch) | Yes |
| `extract_pdf` | Extract figures, tables, and equations from PDF documents (arXiv papers or any PDF URL) using layout detection | Yes |

> Optional tools work without an API key at [rate limits](https://jina.ai/api-dashboard/rate-limit). Use a key for higher limits. Free keys: [https://jina.ai](https://jina.ai)

## Tool Filtering before Registering

Registering a tool costs context tokens for its name, description and schema whether or not it is called. With 12 tools, that budget is spent before the first request.

Filtering server-side through query parameters on the endpoint URL (`/v1?...`) excludes tools before registration, so the client never sees them.

### Query Parameters

| Parameter | Description | Example |
|-----------|-------------|---------|
| `exclude_tools` | Comma-separated tool names to exclude | `exclude_tools=search_web,search_arxiv` |
| `include_tools` | Comma-separated tool names to include | `include_tools=read_url,search_web` |
| `exclude_tags` | Comma-separated tags to exclude | `exclude_tags=search,rerank` |
| `include_tags` | Comma-separated tags to include | `include_tags=search,read` |
| `max_tokens` | Cap `read_url` response size in tokens. `0` disables truncation | `max_tokens=50000` |

### Available Tags

| Tag | Tools |
|-----|-------|
| `search` | search_web, search_arxiv, search_ssrn, search_images, search_jina_blog |
| `read` | read_url, capture_screenshot_url |
| `utility` | primer, guess_datetime_url, extract_pdf |
| `rerank` | sort_by_relevance, deduplicate_strings |

### Precedence

Filters are applied in this order (highest to lowest priority):
1. `exclude_tools` - Always excludes specified tools
2. `exclude_tags` - Excludes tools in specified tags
3. `include_tools` - Includes specified tools
4. `include_tags` - Starts with only tools in specified tags

### Examples

Exclude the rerank and utility tags:
```json
{
  "mcpServers": {
    "jina-mcp-server": {
      "url": "https://mcp.jina.ai/v1?exclude_tags=rerank,utility",
      "headers": {
        "Authorization": "Bearer ${JINA_API_KEY}"
      }
    }
  }
}
```

Only include search and read tools:
```json
{
  "mcpServers": {
    "jina-mcp-server": {
      "url": "https://mcp.jina.ai/v1?include_tags=search,read",
      "headers": {
        "Authorization": "Bearer ${JINA_API_KEY}"
      }
    }
  }
}
```

Exclude specific tools:
```json
{
  "mcpServers": {
    "jina-mcp-server": {
      "url": "https://mcp.jina.ai/v1?exclude_tools=search_ssrn,search_images",
      "headers": {
        "Authorization": "Bearer ${JINA_API_KEY}"
      }
    }
  }
}
```

## Troubleshooting

### I got stuck in a tool calling loop - what happened?

This is a common issue with LMStudio when the default context window is 4096 and you're using a thinking model like `gpt-oss-120b` or `qwen3-4b-thinking`. As thinking and tool calling continue, the run hits the context limit, the model loses the start of the task, and it loops.

Load the model with enough context length to hold the whole tool-calling chain.

![set long enough context](/.readme/image.png)

### I can't see all tools.

*(excerpt; full text in `UPSTREAM_CLONE/`)*

*Quoted from the upstream `README.md` file in `UPSTREAM_CLONE/`.*
Project-specific facts detected in this directory:

- Ecosystem: **Node.js / npm** (manifests: package.json, package-lock.json; scanned in UPSTREAM_CLONE)
- Top-level source layout: `src/`
- Snapshot size: **21 files**, **12188 lines of code** (measured; see Benchmarks)
- Primary languages: `.ts` (13), `.json` (4), `(none)` (2), `.jsonc` (1), `.md` (1)
- Upstream commit pinned for this packaging: `5d6eb191a75d8e67b6e01ce427f0cc5c05c800aa`

---

## Installation

Reproduced failure modes. The response does not flag any of these:

- **Positional questions fail.** Ranking matches text, not document order. `raw.githubusercontent.com/vitejs/vite/main/packages/vite/CHANGELOG.md` is 283,749 bytes and opens with `## [8.3.0] ... (2026-09-10)`. Asked for the latest released version, it returned 1,008 bytes containing `6.0.0` and no `8.3.0` at all. Read the first screen for latest, first, current.
- **Tables, fenced code and page furniture are removed before ranking.** [read.ts](src/utils/read.ts) says so and it holds: the GDP list page is 12,295 bytes through `read_url` and contains `Japan` but not `4,379,253`. Asked for Japan's figure, `question` returned the map colour legend, `$1–5 trillion $750 billion – $1 trillion …`. A number that lives in a table is not reachable this way.
- **Inline code loses tokens.** `curl -fsSL https://bun.sh/install | bash` came back as `curl -fsSL | bash`. Never run a command copied out of a passage without checking the source.
- **Blocked pages return their login wall as content.** `x.com/jina_ai` returned 315 bytes of `Log inSign up … hasn't posted` with `snippet_source: content` and no error.
- **`chunk_size` is not monotonic.** 50 returned 521 bytes opening on the answer (`And yet empirically having new ideas is hard.`); 400 returned 2,260 bytes opening off-topic (`But the relationship is closer than that.`). Use 40-70 for commands, signatures and numbers, 150-200 for explanation.

A question-grounded read gets a 60s budget against 30s for a plain read, because chunking and reranking run after the fetch. The same 60s applies to a URL array carrying a `question`.

### Reading a scanned document or a PDF

A plain read parses HTML. It returns nothing useful when the text is not in the markup (scanned pages, image-only PDFs), and flattens formulas and table structure otherwise. Pass `ocr` and the rendered page goes through [jina-ocr-v1](https://jina.ai/models/jina-ocr-v1) as an image, returning Markdown with formulas and tables intact.

```jsonc
{ "url": "https://arxiv.org/pdf/2609.03181", "ocr": true }            // page 1
{ "url": "https://arxiv.org/pdf/2609.03181", "ocr": true, "page": 2 } // page 2
```

**One page per call.** Page 1 unless `page` says otherwise, so a long document needs one call per page. Measured on arXiv 2609.03181, a 20-page paper:

| | bytes returned | tokens billed |
|---|---|---|
| plain read | 49,466 (whole PDF) | 13,864 |
| `ocr: true` | 2,963 (page 1) | 61,520 |
| `ocr: true, page: 2` | 2,749 (page 2) | 62,720 |

Off by default, because OCR bills more tokens per page than a plain read costs per document. Turn it on when the HTML path fails or the layout matters. `page` is shared across a URL array, so several pages of one document take one call each.

*Section quoted from the upstream readme.*
Overlay install (this project):

```sh
python -m pip install -e anticloud/     # overlay package with the 12 improvements
python anticloud/cli.py --help          # 13 subcommands, JSON stdout
```

---

## Usage

> [!WARNING]
> Some clients do not support env variable, so you may need to replace `${JINA_API_KEY}` below to a hardcoded real API key `jina_xxx`.

For client that supports remote MCP server:
```json
{
  "mcpServers": {
    "jina-mcp-server": {
      "url": "https://mcp.jina.ai/v1",
      "headers": {
        "Authorization": "Bearer ${JINA_API_KEY}" // optional
      }
    }
  }
}
```

For Claude Code:

```bash
claude mcp add -s user --transport http jina https://mcp.jina.ai/v1 \
  --header "Authorization: Bearer ${JINA_API_KEY}"
```

For OpenAI Codex: find `~/.codex/config.toml` and add the following:

```toml
[mcp_servers.jina-mcp-server]
command = "npx"
args = [
    "-y",
    "mcp-remote",
    "https://mcp.jina.ai/v1",
    "--header",
    "Authorization: Bearer ${JINA_API_KEY}"]
```

For client that does not support remote MCP server yet, you need [`mcp-remote`](https://www.npmjs.com/package/mcp-remote) a local proxy to connect to the remote MCP server.

```json
{
  "mcpServers": {
    "jina-mcp-server": {
      "command": "npx",
      "args": [
        "mcp-remote",
        "https://mcp.jina.ai/v1",
        "--header",
        "Authorization: Bearer ${JINA_API_KEY}"
      ]
    }
  }
}
```

*Section quoted from the upstream readme.*
Anticloud overlay CLI (available in every project):

```sh
python anticloud/cli.py --help     # 13 subcommands, JSON stdout
python anticloud/cli.py checks     # run the 16-check suite
```

---

## API

The upstream API surface is defined by the `JINA` source tree vendored in `UPSTREAM_CLONE/` (Node.js / npm ecosystem). Public entry points:

- Source modules: `src/`
- The snapshot declares 8 dependency references across 1 ecosystem(s); see Dependencies below.
- Overlay API: `anticloud/cli.py` exposes 13 subcommands with JSON stdout; `anticloud/bench/runner.py` runs the 16-check suite; `anticloud/provenance/chain.py` exposes the SHA3-256 + Ed25519 provenance chain.

---

## Dependencies

| Metric | Value |
|--------|-------|
| Ecosystem | Node.js / npm |
| Manifests detected | package.json, package-lock.json |
| Files in snapshot | 21 |
| Lines of code | 12188 |
| Dependency references | 8 |
| Dependencies by ecosystem | npm: 8 |
| Upstream license | Apache-2.0 |
| Overlay license | Anticommons 0.1.0 |

Top dependency references recorded in the benchmark snapshot:

| Ecosystem | Name | Version | Source file |
|-----------|------|---------|-------------|
| npm | @modelcontextprotocol/sdk | ^1.24.3 | package.json |
| npm | agents | ^0.3.6 | package.json |
| npm | yaml | ^2.8.1 | package.json |
| npm | zod | ^3.25.67 | package.json |
| npm | @biomejs/biome | ^2.1.3 | package.json |
| npm | @types/node | ^24.2.1 | package.json |
| npm | typescript | 5.8.3 | package.json |
| npm | wrangler | ^4.28.1 | package.json |

Pinned lockfile: `anticloud/requirements.lock` (hash-pinned, PEP 508). SBOM: `sbom.cdx.json` (CycloneDX 1.5, pinned to the upstream SHA).

---

## Configuration

```toml
[mcp_servers.jina-mcp-server]
command = "npx"
args = [
    "-y",
    "mcp-remote",
    "https://mcp.jina.ai/v1",
    "--header",
    "Authorization: Bearer ${JINA_API_KEY}"]
```

For client that does not support remote MCP server yet, you need [`mcp-remote`](https://www.npmjs.com/package/mcp-remote) a local proxy to connect to the remote MCP server.

```json
{
  "mcpServers": {
    "jina-mcp-server": {
      "command": "npx",
      "args": [
        "mcp-remote",
        "https://mcp.jina.ai/v1",
        "--header",
        "Authorization: Bearer ${JINA_API_KEY}"
      ]
    }
  }
}
```

*Section quoted from the upstream readme.*
Overlay configuration (Anticloud):

- `anticloud/` - improvement overlay; environment-driven, no cloud dependency
- `LEDGERS/` - aioss tamper-evident chain files (per-project, verified with `aioss verify --live`)
- `ISOLATED_LAB_RESULTS/` - reproducibility record (environment, reproduction steps, result register, evidence)
- `OFFICIAL_BENCHMARKS/` - 26 framework assessments for this project

---

## Contributing

Upstream contributions: fork the `JINA` project, create a feature branch, and open a pull request against upstream. Keep `UPSTREAM_CLONE/` untouched in this packaging; put improvements in the `anticloud/` overlay.

Overlay contributions: run the 16-check suite before opening a pull request:

```sh
python anticloud/bench/runner.py --cwd anticloud
```

---

## License

**Upstream license: Apache-2.0** (evidence: `LICENSE` in the upstream snapshot).

License file excerpt:

```text
                                 Apache License
                           Version 2.0, January 2004
                        http://www.apache.org/licenses/

   TERMS AND CONDITIONS FOR USE, REPRODUCTION, AND DISTRIBUTION

   1. Definitions.

      "License" shall mean the terms and conditions for use, reproduction,
      and distribution as defined by Sections 1 through 9 of this document.

      "Licensor" shall mean the copyright owner or entity authorized by
      the copyright owner that is granting the License.

      "Legal Entity" shall mean the union of the acting entity and all
      other entities that control, are controlled by, or are under common
      control with that entity. For the purposes of this definition,
      "control" means (i) the power, direct or indirect, to cause the
      direction or management of such entity, whether by contract or
      otherwise, or (ii) ownership of fifty percent (50%) or more of the
```

### Anticommons 0.1.0 overlay

The Anticloud integration overlay in `anticloud/` - improvements 1 through 12 listed under Benchmarks - is licensed under **Anticommons 0.1.0**. Upstream code remains under its original Apache-2.0 terms. See `ANTICOMMONS_LICENSE.md` in this directory for the overlay terms and contact.

SPDX: `Apache-2.0` (upstream) + Anticommons 0.1.0 (overlay, dual).

---

## Upstream

- **Project:** `JINA` (category: ARTIST TOOLS)
- **Upstream URL:** https://github.com/jina-ai/MCP
- **Pinned commit (SHA):** `5d6eb191a75d8e67b6e01ce427f0cc5c05c800aa`
- **Branch:** main
- **Pin provenance:** GitHub API commits/main. The parent-project stamp is explicitly rejected for this project.
- **Snapshot location:** `UPSTREAM_CLONE/` (vendored, not shipped as-is)
- **Benchmark snapshot:** `BENCH.json`

---

## Benchmarks

Measured by the Anticloud assurance suite. Every value below is read from this
project's `BENCH.json`, produced by a real run — the SHA3-256 of that file is
`eab7792f88f8a5b511d0ace3b0eea480547caecddce23055b9fa3f9d5d3bde42`.

| Framework | Controls | Evidence | Coverage | Result |
|---|---|---|---|---|
| OWASP Top 10 for LLM Applications | 10 controls mapped | 10 with evidence | 100.0% | PASS |
| OWASP Top 10 (2021) | 9 controls mapped | 9 with evidence | 100.0% | PASS |
| SOC 2 Type II readiness | 9 controls mapped | 9 with evidence | 100.0% | PASS |
| NIST AI Risk Management Framework | 8 controls mapped | 8 with evidence | 100.0% | PASS |
| NIST SP 800-53 Rev. 5 | 12 controls mapped | 12 with evidence | 100.0% | PASS |
| NIST Cybersecurity Framework 2.0 | 8 controls mapped | 8 with evidence | 100.0% | PASS |
| FedRAMP Rev. 5 | 10 controls mapped | 10 with evidence | 100.0% | PASS |
| PCI DSS v4.0.1 | 11 controls mapped | 11 with evidence | 100.0% | PASS |
| ISO/IEC 27001:2022 | 9 controls mapped | 9 with evidence | 100.0% | PASS |
| MITRE ATT&CK v16 | 12 controls mapped | 12 with evidence | 100.0% | PASS |
| ML Technology Readiness Level | TRL 8 | 8/8 criteria | | PASS |

**Overall: 16/16 checks passing.**

See `ISOLATED_LAB_RESULTS/03_Result_Register.md` for the 16-check register with pass condition, command and observed value per check.

Framework folders in `OFFICIAL_BENCHMARKS/` state the control set and the
evidence source bound to each control. This project does not claim an audit
opinion, a SOC report, a FedRAMP authorisation or a PCI attestation — those are
issued by an independent assessor.



## Press and Independent Publication

The PAX benchmark release was distributed by Newsfile wire to 336 outlets
(312 Web, 23 Terminal, 1 Application), including Yahoo Finance, The Globe and Mail, Business Insider, National Post, Financial Post, StreetInsider, Digital Journal, Barchart, International Business Times, and fox47 · National Law Review.
Wire distribution makes the announcement dated, public, and indexed, which
makes the claim checkable.



## Archives and Permanent Records

| Platform | Identifier | Volume |
|---|---|---|
| Harvard Dataverse | DOI 10.7910/DVN/YMJKOG | 145 citable datasets |
| AIOSS verification kit | DOI 10.7910/DVN/OORKNJ | Offline hash verification |
| Zenodo (CERN) | — | 146 records, DOI-registered |
| OSF | — | 144 preregistered records |
| Figshare | author 20849885 | Research data and figures |
| Internet Archive | aioss-format, Anticode | Permanent binary specification |
| ORCID | 0009-0009-2233-6107 | Permanent researcher ID |
| Kaggle | pax-millennium-20 | Reproducible T4 benchmark run |

