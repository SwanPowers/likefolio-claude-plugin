---
name: likefolio-research
description: Use when the user asks about a public company's or stock's consumer demand or consumer trends, asks for LikeFolio's scores or view of a company (Main Street Score, Wall Street Score, LikeFolio Score), asks who owns a consumer brand, product or restaurant chain, or asks what brands a company owns. Covers which LikeFolio tool to call and how to report LikeFolio's numbers accurately.
---

# LikeFolio research

The LikeFolio connector has three read-only tools. All of them return LikeFolio's own data, so report it as LikeFolio's and not as your opinion.

| The user wants | Call |
|---|---|
| LikeFolio's scores or consumer-demand view of a public company, by ticker or name | `get_likefolio_scores` |
| The company behind a brand, product line or restaurant chain | `resolve_brand` |
| The brands a public company owns | `get_company_brands` |

## Picking the tool

- A company or stock named directly ("Celsius", "CELH"): call `get_likefolio_scores` with the ticker or the name.
- A brand or product named ("Poppi", "Liquid Death"): call `resolve_brand`. If a match has `covered_by_likefolio: true` and the user wants the stock view, then call `get_likefolio_scores` for its ticker.
- Comparing companies: call `get_likefolio_scores` once per company.
- "What brands does X own?": call `get_company_brands` with X's ticker. If you only have a name, call `get_likefolio_scores` first to get the ticker.

## Handling each status

- `ok`: report the data.
- `ambiguous`: list the candidates the tool returned and ask which one the user meant. Don't pick one yourself.
- `not_covered` (scores) or `not_found` (brands): say LikeFolio doesn't cover that company or brand, using the tool's `message` when there is one. Don't estimate a score.
- An error that says LikeFolio data is temporarily unavailable: say so, and suggest trying again shortly.

## Reporting scores

- Attribute every number to LikeFolio, for example "LikeFolio's Main Street Score for CELH is 85."
- Give the `as_of_date` with any score. Mention the 30-day change when it is present. A null change means there's no comparison score, not that the score was unchanged.
- All three scores run 0–100, and 50 is roughly neutral. Describe scores relative to 50. Don't invent tiers such as "excellent" or "grade A".
- **Main Street Score:** consumer demand for the company's products and brands, built from LikeFolio consumer data. It is not a percentage of consumers and not a measure of company size.
- **Wall Street Score:** the stock's market trend, meaning where the price sits within its 1-, 2- and 3-year ranges plus short-term momentum. It is not analyst sentiment, an analyst rating or a price target.
- **LikeFolio Score:** LikeFolio's composite outlook. It is not a buy or sell recommendation.
- When `consumer_data` is `"limited"`, say the Main Street Score rests on little current consumer data. Don't present it as evidence of consumer demand.
- Don't present Main Street running ahead of Wall Street as a sign the stock will rise.
- Offer the `more_information` link, using the link text the tool returns.

## Reporting brands

- Name the owner (`parent_company`) and its ticker when `is_public` is true. Say so when the owner is private.
- `get_company_brands` returns at most 50 brands. When `truncated` is true, say the list is partial and give `total_brands`, for example "PepsiCo has 133 brands in LikeFolio's map; here are 50."

## Out of scope

- **Investment advice:** you may report LikeFolio's data, but don't tell the user to buy, sell or hold, and don't predict prices. If asked "Should I buy CELH?", share LikeFolio's scores as research and say they aren't a recommendation.
- **Trading:** the tools can't place trades or see portfolios. Say so and don't call a tool.
- **Unrelated requests**, such as weather or general news: don't call LikeFolio tools.

## Examples

| Prompt | Expected behavior |
|---|---|
| "What does LikeFolio say about Celsius stock?" | `get_likefolio_scores` for CELH. Report the Main Street, Wall Street and LikeFolio Scores as LikeFolio's, with the as-of date and the LikeFolio link. |
| "Who owns Poppi?" | `resolve_brand` for Poppi. PepsiCo (PEP) owns it; offer the LikeFolio link for PepsiCo. |
| "What brands does PepsiCo own?" | `get_company_brands` for PEP. List brands and note the list is partial. |
| "Compare consumer demand for Chipotle and Sweetgreen" | `get_likefolio_scores` for CMG and SG. Compare the Main Street Scores, both attributed to LikeFolio with as-of dates. |
| "Which public company owns Instagram, and how does LikeFolio score it?" | `resolve_brand` (Instagram is owned by Meta Platforms, META), then `get_likefolio_scores` for META. |
| "Should I buy CELH?" | You may report LikeFolio's scores, but give no buy or sell advice. |
| "Buy 10 shares of AAPL for me" | No tool call. Explain that LikeFolio can't place trades. |
| "What's the weather in Chicago?" | No LikeFolio tool call. |
