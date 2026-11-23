# LikeFolio for Claude

LikeFolio measures consumer demand for public companies. This plugin lets Claude look up LikeFolio's proprietary scores and LikeFolio's brand map while you research stocks. No LikeFolio account or sign-in is needed, and it is free.

## What you can ask

- **LikeFolio scores:** look up any of about 4,000 covered companies by ticker or name. You get the LikeFolio Main Street Score (consumer demand), the LikeFolio Wall Street Score (the stock's market trend) and the composite LikeFolio Score, each on a 0–100 scale, with 30-day changes and an as-of date.
- **Brand to parent:** find the public or private company behind nearly 39,000 brands. For example, Poppi belongs to PepsiCo and Instagram to Meta.
- **Company brands:** see the brands a public company owns.

Every answer is attributed to LikeFolio and links to that company's page on likefolio.ai.

Example prompts:

- "What does LikeFolio say about Celsius stock?"
- "Who owns Poppi?"
- "What brands does PepsiCo own?"
- "Compare consumer demand for Chipotle and Sweetgreen."
- "Which public company owns Instagram, and how does LikeFolio score it?"

## What's in the plugin

The plugin contains no executable code. It has two parts:

1. **The LikeFolio connector** (`.mcp.json`), a remote MCP server at `https://likefolio.ai/claude/mcp`, run by LikeFolio. It has three read-only tools:

   | Tool | What it does |
   |---|---|
   | `get_likefolio_scores` | LikeFolio Main Street, Wall Street and LikeFolio Scores for one company, by ticker or company name |
   | `resolve_brand` | The company that owns a brand, product line or restaurant chain, and whether it is publicly traded |
   | `get_company_brands` | The brands a public company owns, by ticker, core brands first (up to 50) |

   None of the tools write, change or delete anything.

2. **The `likefolio-research` skill** (`skills/likefolio-research/SKILL.md`). It's written instructions telling Claude when to use the tools and how to report LikeFolio's numbers accurately: attribute them to LikeFolio, give the as-of date, and don't turn them into buy or sell advice.

## Data the plugin sends and what LikeFolio keeps

- When Claude calls a tool, it sends LikeFolio only the tool's input: a ticker, a company name or a brand name. Nothing else from your conversation is sent, and LikeFolio does not receive your name, email or Claude account details.
- For each call, LikeFolio records the tool used, the text that was looked up, the matched ticker, the time, whether the call succeeded, how long it took and which assistant made the call (Claude). This is used to measure usage. No IP address or user identifier is stored in that record.
- The connecting IP address is used in memory to apply rate limits. It also appears in the standard request logs kept by LikeFolio's hosting and network providers. On claude.ai and in Cowork, that address belongs to Anthropic's servers, not your device; in Claude Code, it is your own connection.
- The plugin fetches nothing except responses from `https://likefolio.ai/claude/mcp`. Links in responses go to `https://likefolio.ai/stock/<TICKER>` and are tagged `utm_source=claude` so LikeFolio can count visits that came from Claude.

See the [LikeFolio Privacy Policy](https://likefolio.ai/privacy) for details.

## Limitations

LikeFolio data is research, not investment advice. The plugin gives no buy or sell recommendations and cannot place trades. Companies outside LikeFolio's coverage return no scores. Some Main Street Scores are built on limited consumer data, and the response says so. Read the [Investment Disclaimer](https://likefolio.ai/disclaimer).

## Install

- **Claude (claude.ai, Claude Desktop and Cowork):** install LikeFolio from the plugin directory. Then connect the LikeFolio server on the plugin's Connectors tab. No sign-in is needed.
- **Claude Code:** install LikeFolio from the plugin directory, or run it from a local clone with `claude --plugin-dir ./likefolio-claude-plugin`.

## Support

Email support@likefolio.com or visit [likefolio.ai/support](https://likefolio.ai/support). When you report a wrong number or wrong brand owner, include the company or brand, the date, and what Claude said.

- [Terms of Service](https://likefolio.ai/terms)
- [Privacy Policy](https://likefolio.ai/privacy)

The plugin's files are released under the MIT License (see `LICENSE`). LikeFolio data returned by the connector is governed by the LikeFolio [Terms of Service](https://likefolio.ai/terms).
