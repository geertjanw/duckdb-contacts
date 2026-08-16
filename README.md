# DuckDB Contacts

There are a few good ways to find GitHub projects that use DuckDB, plus a growing ecosystem of notable ones. 

Here's an overview.

**Where to look**

1. **The `duckdb` GitHub topic** (https://github.com/topics/duckdb) — repos tagged with DuckDB, sortable by stars. You'll find everything from data warehouse builds with PostgreSQL/Snowflake/DuckDB to analytics workflows with cohort analysis and predictive modeling, and local-first AI analytics assistants combining DuckDB with dbt, dlt, and Ibis.

2. **awesome-duckdb** (https://github.com/davidgasquez/awesome-duckdb) — the curated list of DuckDB libraries, tools and resources that the DuckDB team itself uses as its go-to reference.

3. **GitHub search API** — interestingly, the DuckDB team published a post showing how to use the GitHub search repositories API to find repos mentioning DuckDB, then use DuckDB itself to parse and query the results with SQL. If your question was actually "how do I use DuckDB to identify GitHub projects," that post is the recipe: fetch the API's JSON results, then query them directly with `read_json` in DuckDB.

**Notable projects built on DuckDB**

- pg_duckdb / duckdb-postgres — DuckDB-powered Postgres for high-performance apps and analytics
- duckdb-wasm — the WebAssembly version, which reads Parquet, CSV and JSON over HTTP and powers browser-based tools like shell.duckdb.org
- Rill Data (SQL-driven dashboards), Ibis (DataFrame API), Tad (fast tabular data viewer), Honeycomb Maps (browser geospatial analysis on DuckDB-Wasm), and VulcanSQL (data API framework using DuckDB as cache/connector)
- tailpipe (open-source SIEM for log insights) and preswald (Wasm packager for Python data apps)
- MotherDuck's org, which maintains a cookbook of example projects built on MotherDuck plus forks and experimental DuckDB tooling
