# How-To Guide

**For:** SWS DEA — Ryan Lafferty  
**Date:** Oct 1, 2025

---
Related [[Enverus API magic number and tables]]
1073741822 - For Pro to recognize new table 
### Links
##### Field Summaries
Other Enverus Tables are also found here for each feature class in the Enverus API 

E:\GIS\GIS Admin\Enverus Schemas\archive\FieldSummary.xlsx
##### Overview of files that need to be prepped for Dagster for Shaun 

[Enverus Catalog and Ingestion Tracker.xlsx](https://selectenergyservices.sharepoint.com/:x:/r/sites/Corp-DEA/_layouts/15/Doc.aspx?sourcedoc=%7BBB1399A4-BCB3-4F68-98E7-DE87DB9EA2C3%7D&file=Enverus%20Catalog%20and%20Ingestion%20Tracker.xlsx&wdLOR=cD8E91C74-B9F6-4FE2-9362-035277D401F5&fromShare=true&action=default&mobileredirect=true)
##### Git Repo Select ( get with Ken )

[https://git-codecommit.us-east-2.amazonaws.com/v1/repos/sws_enverus_temp](https://git-codecommit.us-east-2.amazonaws.com/v1/repos/sws_enverus_temp "https://git-codecommit.us-east-2.amazonaws.com/v1/repos/sws_enverus_temp")

#### Field Summaries

E:\GIS\GIS Admin\Enverus Schemas\archive\FieldSummary.xlsx

#### Passwords
password for postgres

rlafferty
O8_mb-X=*>T1/O4f 

#### Databases:

![[Table Plus Enverus.png]]


## Quick Start (2 checklists)

### A) Jira for Dev & Data-Pipeline Projects

-  Choose project type: **Company-managed** (recommended for shared workflows) or **Team-managed**
    
-  Create issue types: **Epic, Story, Task, Bug, Spike** (+ custom **Pipeline Job, Dataset** if helpful)
    
-  Set fields: `Environment`, `Service / Domain`, `Data Source`, `Dagster Asset Key`, `Owner`, `SLA`, `Risk`
    
-  Define workflows (statuses) and DoR/DoD
    
-  Connect GitHub + CI/CD; configure PR → Jira automation
    
-  Set up boards (Scrum for sprints, Kanban for flow) + WIP limits
    
-  Create dashboards (Burndown, CFD, Flow, DORA proxy, Quality)
    
-  Add automations for Dagster webhooks & incident creation
    

### B) Postgres (GIS) Prep for Dagster

-  Enable **PostGIS** extension in target DB
    
-  Adopt schemas: `staging`, `core`, `marts` (and optional `sandbox`)
    
-  Name tables/columns `snake_case`, add `id`, `created_at`, `updated_at`, `load_batch_id`
    
-  Declare geometry types with SRID, add **GiST/BRIN** indexes
    
-  Add quality constraints (NOT NULL, CHECK `ST_IsValid`, FK references)
    
-  Partition large, time-based tables; plan incremental keys
    
-  Set **roles & grants** for Dagster/dbt/BI
    
-  Provide **stable views** for apps (Map/Portal)
    
-  Write asset contracts (columns, types, SLAs) and sample data
    

---

## Part 1 — Jira for Project Management (Dev + Data Pipelines)

### 1. Project Setup

**When to use each:**

- **Company-managed:** Shared workflows, custom fields, consistent reporting across squads (recommended for SWS DEA).
    
- **Team-managed:** Lightweight, team-specific; avoid if you need central dashboards.
    

**Project templates:**

- **Software (Scrum)** for development sprints
    
- **Kanban** for ops-heavy data pipelines
    

**Core Issue Types:**

- **Epic** — Outcome level (e.g., "Permian Maps Revamp", "Data Domain: Hydrology")
    
- **Story** — User-centric goal (e.g., "User can filter assets by basin")
    
- **Task** — Work item (e.g., "Create facility_gis table")
    
- **Bug** — Defect (e.g., "Geometry SRID mismatch in pipelines")
    
- **Spike** — Time-boxed research (e.g., "Assess BRIN vs GiST for big poly tables")
    
- _(Optional)_ **Pipeline Job** (one per scheduled job) / **Dataset** (one per table or view)
    

**Key Custom Fields (recommend):**

- `Service / Domain` (e.g., Hydrology, Infrastructure, Marketing Maps)
    
- `Environment` (Dev, Test, Prod)
    
- `Data Source` (Enverus, B3, Portal, FME, DroneDeploy)
    
- `Dagster Asset Key` (e.g., `core/pipelines`, `marts/facility_summary`)
    
- `SLA / Schedule` (cron or schedule name)
    
- `Risk` (Low/Med/High), `Priority`, `Effort` (S/M/L or points)
    
- `Data Sensitivity` (Internal, Confidential)
    

### 2. Workflows & Statuses

**Development Workflow (Scrum):**  
`Backlog → Selected for Sprint → In Progress → In Review → In QA → Done`

**Pipeline Workflow (Kanban/Scrumban):**  
`Intake → Design → Build → Validate (QA/Data) → Schedule/Deploy → Observing → Done`

**Definitions (write these on your project page):**

- **Definition of Ready (DoR):**
    
    - Problem statement, acceptance criteria, dependencies listed, data source identified, estimate assigned, owner assigned.
        
- **Definition of Done (DoD):**
    
    - Code merged, tests passing, dbt/Dagster jobs green, docs updated, dashboards wired, monitoring in place.
        

### 3. Boards

- **Scrum board:** Sprint planning every 1–2 weeks (fits "agile light sprints").
    
- **Kanban board:** WIP limits per column (e.g., 3 in Build, 2 in Validate). Swimlanes by **Epic** or by **Incident**.
    
- **Queues:** Add a triage column or a triage swimlane for new requests.
    

### 4. Issue Templates (copy/paste)

**Story (Dev):**

- _As a_ [role]
    
- _I want_ [capability]
    
- _So that_ [business outcome]
    
- **Acceptance Criteria** (Given/When/Then)
    
- **Non-Functional** (perf, security, observability)
    
- **Links**: PR, design, data model, dashboards
    

**Pipeline Job:**

- **Source → Target** (e.g., `B3 API → staging.b3_activity_raw`)
    
- **Transform** (dbt model names)
    
- **Load** (incremental key, dedupe strategy)
    
- **Schedule / SLA** (cron, windows)
    
- **Dagster Asset Key(s)**
    
- **Runbook** (on-call, retries, backfill steps)
    

### 5. Automation Recipes

- **PR → Issue Progress:** When PR references `JIRA-123` and merges to `main`, transition **In Review → In QA**, add commit link.
    
- **Build Failures → Incident:** If CI fails for `main` or Dagster run fails 3x in 24h, create **Bug** with `Incident` label and assign on-call.
    
- **Dagster Webhook:** On run success/failure, update `Pipeline Job` status field; attach run ID/URL.
    
- **Template Subtasks:** When creating issue type **Pipeline Job**, auto-create subtasks: _Create staging table_, _dbt model_, _tests_, _Backfill_, _Schedule_, _Docs_.
    

### 6. Integrations

- **GitHub:** Smart commits (`JIRA-123 #comment … #time 1h #done`).
    
- **CI/CD:** Post build status back to Jira.
    
- **Dagster:** Webhook to Jira automation; link asset keys in issues.
    
- **Slack:** Post sprint goals, Done column, and incident alerts to a channel.
    

### 7. Reporting & Dashboards

- **Sprint Burndown / Velocity**
    
- **Cumulative Flow Diagram (CFD)** for pipeline bottlenecks
    
- **Control Chart** (lead time)
    
- **Quality**: Bugs opened vs. closed, test coverage proxy
    
- **Operations**: Failed runs by asset, MTTR, SLA adherence
    

### 8. Useful JQL

- Epics with open work:  
    `issuetype = Epic AND status != Done`
    
- All Pipeline Jobs failing in last 24h (with label):  
    `issuetype = "Pipeline Job" AND labels = incident AND updated >= -1d`
    
- Ready for QA, this sprint:  
    `status = "In QA" AND sprint in openSprints()`
    
- All items with Dagster asset key:  
    `"Dagster Asset Key" IS NOT EMPTY`
    

### 9. Permissions & Roles

- **Administrators:** modify workflows/fields
    
- **Maintainers:** create components, versions
    
- **Contributors:** create/edit issues, transition
    
- **Viewers:** read-only dashboards
    

---

## Part 2 — Preparing Postgres (GIS) Tables for Dagster

### 1. Prerequisites

```sql
-- Enable PostGIS once per database
CREATE EXTENSION IF NOT EXISTS postgis;
CREATE EXTENSION IF NOT EXISTS postgis_topology; -- optional
```

**Schema strategy**

- `staging` — raw ingests (append-only where possible)
    
- `core` — cleaned, modeled tables (SCD1/2 as needed)
    
- `marts` — serving/analytics/app views
    
- Optional: `sandbox` for ad-hoc
    

**Naming conventions**

- Tables & columns: `snake_case`
    
- Suffixes: `_stg` (staging), `_dim` (dimension), `_fact` (events/measures)
    
- Geometry columns by feature type: `geom_point`, `geom_line`, `geom_poly`
    

### 2. Standard Columns & Contracts

- `id` UUID (surrogate key)
    
- `source_id` (from upstream, preserved as text)
    
- `created_at`/`updated_at` **timestamptz**
    
- `load_batch_id` (for audit/backfills)
    
- Optional SCD2: `valid_from`/`valid_to` timestamptz, `is_current` boolean
    
- **Geometry**: typed with SRID, e.g., `geometry(LineString, 4326)`
    

### 3. Example DDL — Facilities (Points) & Pipelines (Lines)

```sql
-- Facilities (points)
CREATE TABLE core.facility_sites (
  id uuid PRIMARY KEY DEFAULT gen_random_uuid(),
  source_id text,
  name text NOT NULL,
  facility_type text CHECK (facility_type IN ('DSP','TRE','YARD','OFFICE','CHEM')),
  status text CHECK (status IN ('active','planned','under_construction','retired')),
  capacity_m3_per_day numeric,
  basin text,
  state text,
  geom_point geometry(Point, 4326) NOT NULL,
  created_at timestamptz NOT NULL DEFAULT now(),
  updated_at timestamptz NOT NULL DEFAULT now(),
  load_batch_id text
);

-- Geometry & quality constraints
ALTER TABLE core.facility_sites
  ADD CONSTRAINT facility_sites_geom_valid CHECK (ST_IsValid(geom_point)),
  ADD CONSTRAINT facility_sites_enforce_srid CHECK (ST_SRID(geom_point) = 4326);

-- Indexes
CREATE INDEX facility_sites_geom_gist ON core.facility_sites USING gist (geom_point);
CREATE INDEX facility_sites_state ON core.facility_sites (state);

-- Auto-update updated_at
CREATE OR REPLACE FUNCTION core.set_updated_at() RETURNS trigger AS $$
BEGIN NEW.updated_at = now(); RETURN NEW; END; $$ LANGUAGE plpgsql;
CREATE TRIGGER facility_sites_updated_at
  BEFORE UPDATE ON core.facility_sites FOR EACH ROW
  EXECUTE FUNCTION core.set_updated_at();

-- Pipelines (lines)
CREATE TABLE core.pipelines (
  id uuid PRIMARY KEY DEFAULT gen_random_uuid(),
  source_id text,
  line_name text,
  operator text,
  status text CHECK (status IN ('proposed','approved','under_construction','active')),
  diameter_in numeric,
  commodity text CHECK (commodity IN ('water','oil','gas','produced_water','recycle')),
  geom_line geometry(LineString, 4326) NOT NULL,
  created_at timestamptz NOT NULL DEFAULT now(),
  updated_at timestamptz NOT NULL DEFAULT now(),
  load_batch_id text
);

ALTER TABLE core.pipelines
  ADD CONSTRAINT pipelines_geom_valid CHECK (ST_IsValid(geom_line)),
  ADD CONSTRAINT pipelines_enforce_srid CHECK (ST_SRID(geom_line) = 4326);

CREATE INDEX pipelines_geom_gist ON core.pipelines USING gist (geom_line);
CREATE INDEX pipelines_status ON core.pipelines (status);
```

**Large tables:** consider **partitioning** by `created_at` (monthly) or geography (state/basin). Use **BRIN** on `created_at` for append-only:

```sql
CREATE INDEX facility_sites_created_brin ON core.facility_sites USING brin (created_at);
```

### 4. Staging Pattern & Incremental Loads

```sql
-- Raw staging table (append-only)
CREATE TABLE staging.b3_activity_raw (
  load_batch_id text,
  payload jsonb NOT NULL,
  ingested_at timestamptz DEFAULT now()
);

-- Deduped staging view
CREATE MATERIALIZED VIEW staging.b3_activity AS
SELECT DISTINCT ON ((payload->>'id'))
  (payload->>'id')::text AS source_id,
  payload,
  max(ingested_at) AS latest_at
FROM staging.b3_activity_raw
GROUP BY (payload->>'id'), payload;

CREATE UNIQUE INDEX ON staging.b3_activity (source_id);
```

**Surrogate keys / idempotency**

```sql
-- Example surrogate key from stable business keys
SELECT gen_random_uuid() AS id
FROM (
  SELECT md5(source_id || coalesce(payload->>'updated_at','')) AS key_hash FROM staging.b3_activity
) s;
```

### 5. Data Quality & Governance

- NOT NULL where required; domain `CHECK` constraints
    
- Geometry validity checks; use `ST_MakeValid` in ETL when needed
    
- Foreign keys to reference tables (e.g., `facility_type`, `state`)
    
- Row-level checks for SRID and geometry type
    
- **Audit**: `load_batch_id` ties rows to runs; keep run logs in `ops.run_log` table
    

### 6. Roles & Grants (minimum setup)

```sql
-- Roles
CREATE ROLE role_dagster NOINHERIT;
CREATE ROLE role_dbt NOINHERIT;
CREATE ROLE role_gis_ro NOINHERIT;

-- Users
GRANT role_dagster TO dagster_user;
GRANT role_dbt TO dbt_user;
GRANT role_gis_ro TO portal_reader;

-- Privileges
GRANT USAGE ON SCHEMA staging, core, marts TO role_dagster, role_dbt, role_gis_ro;
GRANT SELECT, INSERT, UPDATE, DELETE ON ALL TABLES IN SCHEMA staging, core TO role_dagster;
GRANT SELECT ON ALL TABLES IN SCHEMA core, marts TO role_gis_ro;
ALTER DEFAULT PRIVILEGES IN SCHEMA core GRANT SELECT ON TABLES TO role_gis_ro;
```

### 7. Views for Apps & GIS

Provide stable, consumer-friendly views (hide raw columns, cast types, format geometries when needed):

```sql
CREATE OR REPLACE VIEW marts.facility_sites_public AS
SELECT
  id,
  name,
  facility_type,
  status,
  basin,
  state,
  ST_Force2D(geom_point) AS geom_point, -- keep 2D for web maps
  created_at
FROM core.facility_sites
WHERE status IN ('active','under_construction');
```

### 8. Maintenance

- Schedule `VACUUM (ANALYZE)` on staging and high-churn tables
    
- Refresh materialized views on successful upstream runs
    
- Periodically `CLUSTER` or `REINDEX` large GiST indexes if bloat is high
    

### 9. Dagster Integration Patterns

**Option A — dbt-first (recommended with your stack)**

- Use dbt for transforms/tests; Dagster orchestrates dbt runs and upstream loads.
    
- Define Dagster **software-defined assets** that map to dbt models and raw ingestion tasks.
    

**Option B — SQL-in-Dagster**

- Use a Postgres IO manager/ops to run SQL.
    

**Sample (Python) — Software-defined assets with Postgres + dbt**

```python
from dagster import AssetExecutionContext, asset, Definitions, ScheduleDefinition, FreshnessPolicy
from dagster_dbt import load_assets_from_dbt_project
from dagster_postgres import PostgresIOManager
import os

io_manager = PostgresIOManager(
    database=os.getenv("PGDATABASE"),
    hostname=os.getenv("PGHOST"),
    username=os.getenv("PGUSER"),
    password=os.getenv("PGPASSWORD"),
    port=int(os.getenv("PGPORT", 5432)),
)

@asset(
    key_prefix=["staging"],
    name="b3_activity_raw",
    freshness_policy=FreshnessPolicy(maximum_lag_minutes=60),
)
def b3_activity_raw(context: AssetExecutionContext):
    # Ingest raw JSON from B3/Enverus to staging
    # Write to staging.b3_activity_raw and return rowcount
    ...

# Load dbt models as assets (core & marts)
dbt_assets = load_assets_from_dbt_project(
    project_dir="./dbt", profiles_dir="./dbt",
)

defs = Definitions(
    assets=[b3_activity_raw, dbt_assets],
    resources={"io_manager": io_manager},
    schedules=[
        ScheduleDefinition(job=dbt_assets.build_job(), cron_schedule="0 * * * *"),
    ],
)
```

**Sensors / Webhooks**

- Sensor listens for upstream file arrival → kick off `b3_activity_raw`
    
- On job failure, post to Jira/Slack and attach `run_id`
    

### 10. Tests & Validation

- **dbt tests**: `unique`, `not_null`, `relationships`, and custom geo tests (`ST_IsValid`, SRID checks)
    
- **Contract tests** for assets: column names/types enforced
    
- **Row-count / delta checks** on incremental loads
    

**Example dbt schema.yml fragment**

```yaml
models:
  - name: facility_sites
    description: Cleaned facility points with geometry and SRID 4326
    columns:
      - name: id
        tests: [not_null, unique]
      - name: geom_point
        tests: [not_null]
      - name: state
        tests: [not_null]
```

---

## Appendices

### A) Sample Request–to–Run Mapping (Pipeline)

1. **Intake (Jira issue created)** → auto subtasks generated
    
2. **Design** → DDL reviewed; DoR met
    
3. **Build** → staging load + dbt model + tests
    
4. **Validate** → data QA & geometry checks green
    
5. **Schedule/Deploy** → Dagster schedule set, runbook linked
    
6. **Observing** → first 7 days monitored; alert thresholds tuned
    
7. **Done** → docs in README/dbt docs; Jira closed
    

### B) Common Pitfalls & Fixes

- **SRID mismatches:** enforce with constraints; fix via `ST_Transform` in ETL
    
- **Invalid geometries:** `ST_IsValid`, `ST_MakeValid`, and simplify where needed
    
- **Slow spatial queries:** add GiST on geometry; consider `ST_SubDivide` for very complex polygons
    
- **Bloat in staging:** use append-only with periodic purge by `ingested_at`
    
- **Unstable downstream schemas:** expose stable **views**; keep raw/core behind them
    

### C) Migration Checklist

- Export current KMZ/shapefiles → load to `staging`
    
- Build core tables with constraints/indexes
    
- Create marts views for Portal/Power BI
    
- Add dbt tests + Dagster assets
    
- Backfill history (batch by date)
    
- Turn on schedules; set alerts; document runbook



# Shaun's Task Outline

Create Schema file (Ryan) - Excel 

QA Schema (Shaun)

Import Schema (create the database table) (Ryan) (?)

Develop DAG asset (Shaun)

Run inital data load (Shaun)

Validate data (Ryan) - (?)

Run update dag (Shaun) 

Validate data (Ryan) (?)

Schedule and Publish Dag (Shaun)

Publish GIS Layer (Shaun)