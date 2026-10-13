# ISHOB | Intelligent Service Hub for Autonomous Bots

[![x402](https://img.shields.io/badge/x402-payments%20enabled-0052FF)](https://www.x402.org)
[![Base](https://img.shields.io/badge/settlement-USDC%20on%20Base-0052FF)](https://base.org)
[![MCP](https://img.shields.io/badge/MCP-Streamable%20HTTP-black)](https://modelcontextprotocol.io)
[![MCP Registry](https://img.shields.io/badge/MCP%20Registry-com.ishob%2Farchive-555)](https://registry.modelcontextprotocol.io/v0/servers?search=com.ishob)

**ISHOB** ([ishob.com](https://ishob.com)) is a pay-per-call data and tools API for AI agents. It exposes **35 MCP tools** and **32 paid HTTP routes**: web extraction, crypto security, company and financial data, weather and location risk, document rendering and an open-licence research archive.

- **No account, no API key.** Paid calls are settled per request with [x402](https://www.x402.org) in **USDC on Base**.
- **Prices from $0.0001 to $0.50 per call.** Calls that fail are not charged.
- **Discovery is free.** Listing tools, searching the archive and fetching price terms cost nothing.

> This repository holds the documentation and registry metadata for the hosted ISHOB server. The service runs at `https://api.ishob.com`; there is nothing to install.

---

## Connect

**MCP endpoint:** `https://api.ishob.com/mcp` (Streamable HTTP, stateless, protocol `2025-11-25`)

### Claude Desktop / Cursor / Windsurf

```json
{
  "mcpServers": {
    "ishob": {
      "url": "https://api.ishob.com/mcp"
    }
  }
}
```

### Claude Code

```bash
claude mcp add --transport http ishob https://api.ishob.com/mcp
```

### Plain HTTP

Every paid tool is also a REST route on `https://api.ishob.com`. Call it unpaid to get an `HTTP 402` with the x402 payment terms (`PAYMENT-REQUIRED` header), sign, and repeat with the `PAYMENT-SIGNATURE` header. Any x402 v2 client (for example [`@x402/fetch`](https://www.npmjs.com/package/@x402/fetch)) does this automatically.

```bash
curl -i "https://api.ishob.com/v1/crypto/prices?tokens=ETH,SOL,BTC"   # -> 402 with price terms
```

### Discovery links

| | |
|---|---|
| Machine manifest (MCP server card) | https://ishob.com/.well-known/mcp.json |
| LLM text summary | https://ishob.com/llms.txt |
| OpenAPI spec | https://api.ishob.com/openapi.json |

---

## Tool catalog

Prices are per successful call, in USDC. MCP tool names are in `code`.

### 1. Web extraction and parsing

| Tool | HTTP route | Price | What it returns |
|---|---|---|---|
| `extract_webpage` | `POST /v1/extract` | $0.001 | A live web page as clean Markdown (robots.txt respected) |
| `site_metadata` | `GET /v1/metadata` | $0.0002 | Title, meta tags, Open Graph, Twitter card, canonical, icons, feeds, JSON-LD |
| `read_feed` | `GET /v1/feed-reader` | $0.0003 | RSS / Atom / JSON Feed as clean JSON items |
| `extract_tables` | `POST /v1/pdf-tables` | $0.001 | Tables from a PDF or HTML page as structured JSON |
| `screenshot` | `POST /v1/screenshot` | $0.001 | PNG / JPEG / WebP screenshot, viewport or full page |
| `site_speed` | `GET /v1/site-speed` | $0.001 | Core Web Vitals (LCP, CLS, FCP, TTFB) from a real browser load |
| `site_audit` | `POST /v1/site-audit` | $0.02 | A–F grade with performance, SEO, security and availability scores, plus a screenshot |
| `domain_intel` | `POST /v1/domain` | $0.0004 | Registration (RDAP), DNS, mail security (SPF/DMARC) and tech stack |
| `ping_status` | `POST /v1/ping-status` | $0.0002 | Up/down, status, response time, redirects, TLS certificate expiry |

### 2. Crypto security and on-chain data

Chains: Ethereum, Base, Arbitrum, Optimism, Polygon, BNB Chain and Solana.

| Tool | HTTP route | Price | What it returns |
|---|---|---|---|
| `token_report` | `POST /v1/crypto/token-report` | $0.05 | 0–100 safety score and verdict: honeypot, taxes, owner privileges, holders, liquidity |
| `wallet_report` | `GET /v1/crypto/wallet-report` | $0.03 | Address reputation, risky approvals, scam tokens held, full portfolio, verdict |
| `token_audit` | `POST /v1/crypto/token-audit` | $0.001 | Fast token scam check with severity-ranked flags |
| `simulate_tx` | `POST /v1/crypto/simulate-tx` | $0.0015 | What a transaction will do before signing: assets in/out, approvals, revert reason |
| `wallet_portfolio` | `GET /v1/crypto/portfolio` | $0.001 | Holdings with USD values per chain |
| `swap_route` | `GET /v1/crypto/swap-route` | $0.001 | Best DEX route and quote (quotes only; nothing is sent) |
| `crypto_prices` | `GET /v1/crypto/prices` | $0.001 | Prices derived from on-chain DEX pools, plus Aave yields |
| `gas_tracker` | `GET /v1/crypto/gas-tracker` | $0.0001 | Slow / standard / fast fees and USD cost of a transfer or swap |
| `contract_abi` | `GET /v1/crypto/contract-abi` | $0.001 | Verified contract ABI with proxy resolution |

Token and address reputation data: Powered by GoPlus Security.

### 3. Company and financial data

| Tool | HTTP route | Price | What it returns |
|---|---|---|---|
| `company_snapshot` | `GET /v1/company-snapshot` | $0.05 | SEC fundamentals, recent filings, official website and a live website/domain check |
| `finance_snapshot` | `GET /v1/finance` | $0.001 | Fundamentals and ratios from the latest annual SEC filing |

### 4. Weather, location and geodata

| Tool | HTTP route | Price | What it returns |
|---|---|---|---|
| `location_report` | `GET /v1/location-report` | $0.01 | Place, weather risk, local time, sunrise/sunset and country facts in one call |
| `weather_risk` | `GET /v1/weather` | $0.001 | Forecast with drone-safe, outdoor-event, wind, rain and frost flags |
| `geocode` | `GET /v1/geocode` | $0.001 | Forward and reverse geocoding with country, population and timezone |
| `get_dataset` | `GET /v1/dataset/{key}` | $0.0001 | Open datasets (world cities, countries) with licence and attribution |

### 5. Documents and AI utilities

| Tool | HTTP route | Price | What it returns |
|---|---|---|---|
| `render_document` | `POST /v1/render-doc` | $0.001 | Markdown to PDF, Word DOCX or HTML (download link valid 24 h) |
| `embed_text` | `POST /v1/embed` | $0.0002 | Text embeddings (BGE models), up to 100 texts per call |

### 6. Research archive (open-licence papers)

| Tool | HTTP route | Price | What it returns |
|---|---|---|---|
| `research_brief` | `POST /v1/research-brief` | $0.05 | Best papers on a topic with citations, plus an AI summary citing only those papers |
| `search_specs` | `POST /v1/search` | $0.001 | Full-text search with extracted technical specifications |
| `query_specs` | `POST /v1/specs` | $0.001 | Specification table filtered by quantity, unit and value range |
| `get_document` | `GET /api/document/{id}` | $0.001 | Paper metadata |
| `get_fulltext` | `GET /api/document/{id}/fulltext` | $0.01 | Structured full text with licence |
| `get_references` | `GET /api/document/{id}/references` | $0.002 | Parsed reference list |
| — | `GET /api/topic/{slug}/content` | $0.50 | Bulk JSONL metadata for a whole topic |
| `search_archive` · `list_topics` · `get_catalog_page` | `/api/search` · `/api/topics` · `/api/catalog` | Free | Search and browse the archive |

Paper text is only served for licences that allow reuse (CC0, public domain, CC BY, CC BY-SA). Sources and licences: [ishob.com/data-rights](https://ishob.com/data-rights).

---

## Payments

| | |
|---|---|
| Protocol | x402 v2, scheme `exact` |
| Network | Base mainnet (`eip155:8453`) |
| Asset | USDC |
| Receiving wallet | `0x906F87F107B4675dD890b3f8EB2d51885E50e09d` |
| When you pay | Only after the tool succeeds; failed calls are not settled |

Over MCP, a paid tool first returns its payment terms; the client signs and repeats the call with `_meta["x402/payment"]`, and the result arrives with a settlement receipt.

---

## Listings

- Official MCP Registry: [`com.ishob/archive`](https://registry.modelcontextprotocol.io/v0/servers?search=com.ishob)
- Smithery: [alicefancy6/ISHOB](https://smithery.ai/servers/alicefancy6/ISHOB)
- PayAPI Market (verified paid route): [Wallet Risk Report](https://payapi.market/api/wallet-risk-report)
- Hugging Face (tool catalog dataset): [za3t3r/ishob-mcp-tools](https://huggingface.co/datasets/za3t3r/ishob-mcp-tools)

## Support

- Website and docs: [ishob.com](https://ishob.com)
- Email: contact@ishob.com

## License

The documentation in this repository is released under the [MIT License](LICENSE). Data returned by the API carries its own licence and attribution, listed at [ishob.com/data-rights](https://ishob.com/data-rights).
