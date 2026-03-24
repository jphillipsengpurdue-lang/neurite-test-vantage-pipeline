# [neurite-test] Vantage Data Pipeline

Apache Spark-based data ingestion and enrichment pipeline for the Vantage platform.

## Architecture

**Runtime:** Apache Spark 3.4 on EMR Serverless (us-east-1)
**Storage:** S3 for raw data, Delta Lake for processed data
**Orchestration:** Apache Airflow 2.7 (MWAA)
**Database:** PostgreSQL 15 (RDS) — pipeline metadata and job state

> ⚠️ **CRITICAL:** We are blocked on Spark 3.5 upgrade. EMR Serverless does not yet
> support Spark 3.5 in us-east-1. This is causing OOM failures for enterprise orgs.
> Tracked in: VANT-5 (Linear), GitHub Issue #1

## Daily Jobs

| Job | Schedule | Owner | Status |
|---|---|---|---|
| raw-ingestion | 02:00 UTC | Alex Park | ⚠️ Failing for enterprise |
| enrichment | 03:30 UTC | Jordan Liu | ⚠️ Blocked on Spark upgrade |
| aggregation | 05:00 UTC | Jordan Liu | ✅ Stable |
| cleanup | 06:00 UTC | Alex Park | ✅ Stable |

## Known Issues

1. Spark 3.4 join optimizer regression — large left joins OOM for orgs > 50k events/day
2. No retry logic on transient 429 failures — entire job fails on any upstream 429
3. Memory ceiling set too low for enterprise tier — temporary fix: manual cluster migration

## Upgrade Path

Waiting for EMR Serverless Spark 3.5 support (AWS ETA: April 2026).
Interim workaround: EMR on EC2 Spot for enterprise orgs (+$800/month).

## Contact

Data team: alex.park@vantage.io, jordan.liu@vantage.io | Slack: #vantage-engineering
