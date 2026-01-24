# TRON Blockchain Forensics - MCP Server

An AI-powered investigation tool for tracing USDT flows on the TRON network. Built using the Model Context Protocol (MCP) to enable natural language orchestration of complex forensic workflows.

## What It Does

This tool helps investigators trace cryptocurrency movements by:

- **Following the money** — Multi-hop transaction tracing upstream (fund sources) and downstream (fund destinations)
- **Identifying exchange touchpoints** — Automatically flags known exchanges and deposit addresses for subpoena targeting
- **Detecting automation** — Behavioral analysis distinguishes human-controlled addresses from programmatic/bot activity
- **Generating reports** — Produces investigation summaries and flow visualizations

## Architecture

```
┌─────────────────────────────────────────────────────────┐
│                   Claude / LLM Client                    │
│                                                          │
│   "Trace where funds from this address went"            │
└─────────────────────┬────────────────────────────────────┘
                      │ MCP Protocol
                      ▼
┌─────────────────────────────────────────────────────────┐
│                    MCP Server                            │
│                                                          │
│   Tools:                     Memory:                     │
│   • get_account_summary      • Session-scoped storage   │
│   • analyze_transaction_flow • Full results preserved   │
│   • get_activation_funding   • Summaries to LLM         │
│   • generate_report                                      │
└─────────────────────┬────────────────────────────────────┘
                      │
                      ▼
┌─────────────────────────────────────────────────────────┐
│              Forensic Analysis Engine                    │
│                                                          │
│   • Multi-hop tracing with confidence scoring           │
│   • Consolidation/split pattern detection               │
│   • Behavioral analysis (dwell time, cadence)           │
│   • Known entity attribution database                   │
└─────────────────────────────────────────────────────────┘
```

## Key Technical Features

| Feature | Description |
|---------|-------------|
| **Agentic AI Integration** | MCP server exposes tools that LLMs can orchestrate autonomously |
| **Confidence Scoring** | Transactions scored 0-100 based on match quality (exact, consolidation, split) |
| **Behavioral Detection** | Identifies programmatic wallets via timing patterns and threshold analysis |
| **Memory Management** | Full trace data stored server-side; only summaries sent to LLM context |
| **Terminal Detection** | Automatically stops tracing at exchanges, high-volume addresses, deposit addresses |

## Sample Workflow

1. **Reconnaissance** — Analyze target address: wallet type, transaction volume, known entity check
2. **Funding Analysis** — Automated upstream and downstream gas tracing for attribution (Which address funded my target, and which addresses did my target fund?)
3. **Flow Tracing** — Multi-hop USDT tracing with configurable depth and amount thresholds
4. **Reporting** — Generate markdown report with exchange touchpoints and law enforcement recommendations

## Tech Stack

- **Python** — Core forensic analysis engine (~4,000 lines)
- **MCP SDK** — Model Context Protocol server implementation
- **TronScan API** — Blockchain data source
- **Docker** — Containerized deployment

## Demo & Source Code

*Live demo URL and source available upon request*
