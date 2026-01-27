# TRON Forensics MCP Server

A Model Context Protocol (MCP) server providing blockchain forensic analysis tools for investigating TRON USDT transactions. Designed for use with Claude Desktop, Claude.ai, or any MCP-compatible client. *Code and demo available upon request.* 

## Features

- **Address Analysis** - Identify address types, known exchanges, and behavioral patterns
- **Funding Chain Tracing** - Discover who funded an address and what addresses it funded
- **Multi-hop Transaction Flow** - Trace USDT movements upstream and downstream across multiple hops
- **Exchange Touchpoint Detection** - Identify where funds touched regulated entities
- **Timezone Activity Analysis** - Identify likely/unlikely user timezones based on transaction timing patterns
- **Report Generation** - Markdown reports, Excel workbooks, and flow diagrams
- **Cloud Storage** - Automatic upload to Cloudflare R2 for downloadable reports (when configured)

## Architecture

```
┌─────────────────────────────────────────────────────────────────┐
│                   MCP Client (Claude Desktop/Claude.ai)         │
│                                                                 │
│   User: "Investigate this TRON address: TDFtJtyL..."           │
│                           │                                     │
└───────────────────────────┼─────────────────────────────────────┘
                            │ MCP Protocol (stdio or SSE)
                            ▼
┌─────────────────────────────────────────────────────────────────┐
│                    server.py (MCP Server)                       │
│                                                                 │
│   ┌─────────────┐    ┌─────────────┐    ┌─────────────┐        │
│   │  Tools      │    │   Session   │    │  Cloud R2   │        │
│   │             │    │   Memory    │    │  Storage    │        │
│   │ Analysis    │    │             │    │  (optional) │        │
│   │ Tracing     │    │ Stores full │    │             │        │
│   │ Reporting   │    │ results     │    │ Report      │        │
│   └──────┬──────┘    └─────────────┘    │ uploads     │        │
│          │                               └─────────────┘        │
│          ▼                                                      │
│   ┌─────────────────────────────────────────────────────────────┐
│   │  api_functions.py  │  trace_flow.py  │  reports.py         │
│   └─────────────────────────────────────────────────────────────┘
│                           │                                     │
└───────────────────────────┼─────────────────────────────────────┘
                            │ HTTPS
                            ▼
                    ┌───────────────┐
                    │  TronScan API │
                    └───────────────┘
```

## Available Tools

### Discovery
| Tool | Purpose |
|------|---------|
| `get_investigation_guide` | Get workflow guidance and demo scenarios |

### Reconnaissance
| Tool | Purpose |
|------|---------|
| `get_account_summary` | Initial analysis of an address |
| `get_activation_funding` | Find who funded the address |
| `get_funded_accounts` | Find accounts this address funded |
| `analyze_activity_timezone` | Identify likely/unlikely user timezones from transaction timing |

### Transaction Tracing
| Tool | Purpose |
|------|---------|
| `analyze_transaction_flow` | Deep multi-hop USDT tracing |
| `enhance_with_counterparty_funding` | Add funding source data to counterparties |
| `enrich_with_funding_data` | Enrich trace data with funding info |
| `extract_exchange_touchpoints` | List all exchange terminals found |

### Report Generation
| Tool | Purpose |
|------|---------|
| `generate_all_investigation_reports` | Generate all applicable reports at once |
| `generate_markdown_investigation_report` | Create comprehensive markdown report |
| `generate_excel_investigation_report` | Create Excel workbook with all data |
| `generate_funding_diagram_report` | Create TRX funding relationship diagram |
| `generate_trace_diagram_report` | Create USDT flow trace diagram |

### Session Management
| Tool | Purpose |
|------|---------|
| `list_investigation_data` | See what's stored in memory |
| `clear_investigation_memory` | Reset for new investigation |

## Setup

### Local (Claude Desktop)

1. **Install dependencies:**
   ```bash
   pip install -r requirements.txt
   ```

2. **Set environment variables** (create `.env` file):
   ```bash
   TRONSCAN_API_KEY=your-api-key-here
   ```

3. **Configure Claude Desktop** (`claude_desktop_config.json`):
   ```json
   {
     "mcpServers": {
       "tron-forensics": {
         "command": "python",
         "args": ["/full/path/to/server.py"],
         "env": {
           "TRONSCAN_API_KEY": "your-api-key-here"
         }
       }
     }
   }
   ```

4. **Restart Claude Desktop**

### Cloud Deployment (Railway/Docker)

The server supports SSE transport for cloud deployment:

```bash
# Build
docker build -t tron-forensics-mcp .

# Run
docker run -p 8000:8000 \
  -e TRONSCAN_API_KEY=your-key \
  -e MCP_TRANSPORT=sse \
  tron-forensics-mcp
```

**Environment variables for cloud:**
| Variable | Purpose |
|----------|---------|
| `TRONSCAN_API_KEY` | TronScan API key (required) |
| `MCP_TRANSPORT` | `sse` for cloud, `stdio` for local |
| `R2_ACCOUNT_ID` | Cloudflare account ID (optional) |
| `R2_ACCESS_KEY_ID` | R2 access key (optional) |
| `R2_SECRET_ACCESS_KEY` | R2 secret key (optional) |
| `R2_BUCKET_NAME` | R2 bucket name (optional) |
| `R2_PUBLIC_URL` | Public URL for downloads (optional) |

## Usage Example

Start with:
> "Run the investigation guide tool to see what this can do."

Or investigate directly:
> "Investigate this TRON address: TDFtJtyLPgN3oWUoHh23oJox3T5V5nR11K"

Typical workflow:
1. `get_account_summary` - Initial reconnaissance
2. `get_activation_funding` - Trace funding source
3. `get_funded_accounts` - Find child addresses
4. `analyze_transaction_flow` - Multi-hop tracing
5. `enhance_with_counterparty_funding` - Expand exchange touchpoints
6. `analyze_activity_timezone` - Identify likely user timezone (optional, requires 100+ outbound transfers)
7. `generate_all_investigation_reports` - Create downloadable reports

## File Structure

```
tron_forensics_mcp/
├── server.py                  # MCP server with tool definitions
├── api_functions.py           # Account info, funding analysis
├── trace_flow.py              # Transaction flow tracing
├── reports.py                 # Report generation, visualization
├── security.py                # Input validation, error sanitization
├── address_attribution.csv    # Known entity database
├── system_prompt.md           # System context for Claude
├── Dockerfile                 # Container configuration
├── requirements.txt           # Python dependencies
└── README.md
```

## Security

- Input validation on all address parameters
- API error sanitization (no key leakage)
- Parameter caps to prevent resource exhaustion
- Non-root container execution

## License

MIT

