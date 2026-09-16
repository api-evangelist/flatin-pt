---
name: calculate-portugal-purchase-cost
description: Calculate the taxes and fees due on top of a Portuguese property purchase price — IMT (property transfer tax), Imposto do Selo (stamp duty) and a deed/registration estimate.
api: flatin.pt API
operations:
  - imt_calculate_api_v1_imt_calculate_post
mcp_tools:
  - imt_calculate
---

# Calculate the cost of buying property in Portugal

Use this to tell a buyer what they will pay **on top of the price** for a home
in Portugal, from the official 2026 tables.

## Endpoint

`POST https://flatin.pt/api/v1/imt/calculate` (operationId `imt_calculate_api_v1_imt_calculate_post`) — keyless, no auth.
MCP equivalent: the `imt_calculate` tool on `https://flatin.pt/mcp`.

## Steps

1. Collect the **purchase price** in euros (`value`, required).
2. Ask whether the property is on the **mainland** (`territory: continente`, the
   default) or in the **Azores/Madeira** (`territory: regioes_autonomas`).
3. Ask whether it is the buyer's **own permanent home** (`primary_home`, default
   `true`) and whether the buyer is **35 or under buying a first home**
   (`buyer_is_young`, default `false`) — the reduced young-buyer scale only
   applies when `primary_home` is true.
4. POST the JSON body, e.g. `{"value": 280000, "territory": "continente", "primary_home": true, "buyer_is_young": false}`.
5. Read back the IMT, Imposto do Selo (stamp duty) and the deed/registration
   estimate, and their sum. Amounts come with a source; **show the `attribution`
   field next to the numbers** (a licence condition).

## Rules and errors

- Invalid input returns **422** with `error` and `field` (FastAPI validation
  envelope). Fix the named field and retry — never guess a value.
- Rate limit: **120 requests/min per IP**; on **429**, wait for the seconds in
  the `Retry-After` header before retrying.
- This is a stateless calculation with no side effects — safe to repeat.
