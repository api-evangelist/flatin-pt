---
name: lookup-imi-rate
description: Look up the yearly municipal property tax (IMI) rate for any of Portugal's 308 municipalities, and estimate a property's yearly IMI from its taxable value (VPT).
api: flatin.pt API
operations:
  - imi_rate_api_v1_imi_rates__codigo__get
  - imi_rates_api_v1_imi_rates_get
  - imi_rates_csv_api_v1_imi_rates_csv_get
mcp_tools:
  - imi_rate
  - imi_annual_cost
---

# Look up a Portuguese municipality's IMI rate

Use this to tell someone the yearly IMI rate a municipality charges, and to
estimate the yearly IMI on a specific property.

## Endpoints

- One municipality: `GET https://flatin.pt/api/v1/imi/rates/{codigo}`
  (operationId `imi_rate_api_v1_imi_rates__codigo__get`) — `{codigo}` is a
  four-digit code (`1312`) or a name (`Porto`).
- All municipalities: `GET https://flatin.pt/api/v1/imi/rates`
  (operationId `imi_rates_api_v1_imi_rates_get`), optional `year`.
- Bulk dataset: `GET https://flatin.pt/api/v1/imi/rates.csv`
  (operationId `imi_rates_csv_api_v1_imi_rates_csv_get`), CC BY 4.0 — prefer this
  over calling the API municipality by municipality.

MCP equivalents on `https://flatin.pt/mcp`: `imi_rate` (the rate) and
`imi_annual_cost` (the yearly euros for a property given its VPT).

## Steps

1. Identify the municipality by name or four-digit code.
2. `GET /api/v1/imi/rates/{codigo}`. Read the current `rate` and the `history`
   (rate by tax year plus its `movement`: raised, lowered, unchanged, returned,
   not_comparable).
3. If `available` is `false`, the municipality sets its rate **per parish** — say
   so; do not report the rate as zero.
4. To estimate a property's yearly IMI, ask for the **VPT** (Valor Patrimonial
   Tributário — the tax authority's valuation, **not** the purchase price) and
   multiply by the urban rate, or use the `imi_annual_cost` MCP tool.
5. **Show the `attribution` field next to the numbers.**

## Rules and errors

- An unknown municipality returns **404** with a link to the full list.
- Bad input (e.g. invalid `year`) returns **422**.
- Rate limit: **120 requests/min per IP**; on **429**, honour `Retry-After`.
  For the whole table, fetch the CSV once instead of looping.
