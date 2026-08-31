# Aether Trade — End-to-End Trading Data Platform

Reference project: **web trading app → OLTP order-management system → Snowflake ELT → data mart / reporting**.

## Important architecture rule

Snowflake is **not** the real-time matching engine.

- **Web app** = order ticket / advisor UI (front office).
- **OLTP OMS** (PostgreSQL-style) = order validation, risk checks, matching, positions, cash (true real-time).
- **Snowflake** = landing, ELT, dimensional warehouse, advisor/risk/compliance reporting (near-real-time + batch).

Latency split:

| Path | Latency target |
|---|---|
| Place order → ACK in UI | 50–200 ms (OLTP only) |
| Fill → position in OMS | sub-second (OLTP) |
| Fill → Snowflake RAW | 1–5 minutes (Snowpipe / CDC micro-batch) |
| Fill → MART fact + dashboard | 5–15 minutes (Streams + Tasks) |

## What is in this repo

| Path | Content |
|---|---|
| `docs/` | High-Level Design and Low-Level Design (Word) |
| `sql/oltp/` | OLTP DDL + seed |
| `sql/snowflake/` | RAW / STG / MART DDL, pipes, streams, tasks, MERGE |
| `data/` | ~25,000 execution rows + related masters (CSV) |
| `scripts/generate_sample_data.py` | Reproducible data generator |
| `docs/Aether_Trade_Data_Dictionary.xlsx` | Table and column catalog |
| `diagrams/er_oltp.md` and `er_mart.md` | Mermaid ER diagrams |

## Suggested volumes (this sample)

- 120 customers, 25 advisors, 180 accounts
- 80 securities
- ~8,000 orders
- ~25,000 executions / fills
- Daily position snapshots for 10 business days
