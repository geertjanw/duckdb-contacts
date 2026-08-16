# Illustrating the DuckDB Documentation with Real-World Code from GitHub

A catalogue of docs-page-to-source-code mappings drawn from popular open-source projects tagged [`duckdb`](https://github.com/topics/duckdb) on GitHub.

Prepared 2026-08-16. All code references were verified against the pinned commits below by cloning each repository and inspecting the files directly; links use commit SHAs so line numbers remain stable as the projects evolve.

## 1. Method and scope

Candidate projects were taken from the GitHub topic page for `duckdb` ([github.com/topics/duckdb](https://github.com/topics/duckdb), ~3,200 repositories), prioritising widely used projects that embed DuckDB in materially different ways: a distributed compute framework (smallpond), a record-linkage library (splink), a desktop data viewer (tad), a BI framework that runs DuckDB both in the browser via WebAssembly and on the server via Node.js (evidence), a dataframe library with DuckDB as its default backend (ibis), and a Go-based BI runtime (rill). Each repository was cloned at the commit listed in Section 2, and the code excerpts below were read in context rather than inferred from search snippets.

Each entry names one page (or a tightly related pair of pages) in the [current DuckDB documentation](https://duckdb.org/docs/current/), then points to concrete lines of code that could be cited on that page as a real-world illustration, together with a short note on what the code adds beyond the docs' own synthetic examples.

## 2. Repositories surveyed

| Repository | Language | Commit | Role of DuckDB |
|---|---|---|---|
| [deepseek-ai/smallpond](https://github.com/deepseek-ai/smallpond) | Python | `52ecc5e` | Lightweight distributed data processing framework built on DuckDB and 3FS |
| [moj-analytical-services/splink](https://github.com/moj-analytical-services/splink) | Python | `1abd964` | Probabilistic record linkage with multiple SQL backends; DuckDB is the default |
| [antonycourtney/tad](https://github.com/antonycourtney/tad) | TypeScript | `272ffa0` | Desktop viewer for CSV/Parquet files, powered by DuckDB (Node bindings) |
| [evidence-dev/evidence](https://github.com/evidence-dev/evidence) | JavaScript | `bdf2ce1` | Business-intelligence-as-code; ships DuckDB-Wasm in the browser and the Neo Node client on the server |
| [ibis-project/ibis](https://github.com/ibis-project/ibis) | Python | `799a2a9` | Portable Python dataframe library; DuckDB is its default backend |
| [rilldata/rill](https://github.com/rilldata/rill) | Go | `04d594f` | BI tool whose runtime embeds DuckDB via the Go driver |

## 3. Summary index

| DuckDB docs page / section | Repository | File and line reference |
|---|---|---|
| [CSV Auto Detection](#41-csv-auto-detection--three-points-on-the-sniffer-spectrum) | splink / tad / smallpond | `database_api.py:84`; `csvimport.ts:64–95`; `dataset.py:478–482` |
| [Reading Faulty CSV Files & CSV Tips](#41-csv-auto-detection--three-points-on-the-sniffer-spectrum) | tad | `csvimport.ts:75–95` (`sample_size=-1` retry) |
| [JSON: Loading JSON](#42-loading-json) | smallpond / ibis | `dataset.py:531–535`; `duckdb/__init__.py:544–610` |
| [Parquet Overview & Multiple Files](#43-parquet-overview-and-multiple-files) | smallpond | `dataset.py:677–703` |
| [COPY / Parquet & JSON export, Partitioned Writes](#44-copy--to-writing-json-and-partitioned-writes) | rill / ibis | `model_executor_self_file.go:150–154`; `duckdb/__init__.py:1480–1536` |
| [Connect Overview (in-memory vs. persistent)](#51-connect-overview-in-memory-vs-persistent-databases) | splink | `database_api.py:33–44`; `duckdb_helpers.py:9–40` |
| [Python: Overview & DB API](#52-python-client-connecting-with-a-config-dictionary) | smallpond | `task.py:1774` |
| [Python: Data Ingestion (register)](#53-python-client-data-ingestion-via-register) | splink | `database_api.py:58–75` |
| [Python: Function API (UDFs)](#54-python-client-function-api-user-defined-functions) | smallpond | `udf.py:181–192` |
| [Python: Conversion / Export to Arrow](#55-python-client-conversion--result-sets-to-arrow) | smallpond | `dataset.py:279–323` |
| [Node.js (Neo) client](#56-nodejs-neo-client) | evidence | `datasources/duckdb/index.cjs:147–217` |
| [Go client](#57-go-client) | rill | `duckdbsql.go:50–69`; `rduckdb/db.go:899` |
| [Wasm: Instantiation](#58-wasm-instantiation) | evidence | `client-duckdb/browser.js:38–70`; `node.js:45–60` |
| [Wasm: Data Ingestion](#59-wasm-data-ingestion) | evidence | `client-duckdb/browser.js:135–138` |
| [ATTACH and DETACH](#61-attach-and-detach-postgresql-and-mysql-extensions) | ibis / rill | `duckdb/__init__.py:1194–1215`; `model_executor_self.go:391` |
| [PostgreSQL / MySQL extensions (ATTACH TYPE …)](#61-attach-and-detach-postgresql-and-mysql-extensions) | rill | `model_executor_sqlstore_self.go:110–125` |
| [CREATE SCHEMA / SET](#62-create-schema-set-and-friendly-sql) | splink | `database_api.py:46–52` |
| [Friendly SQL (FROM-first)](#62-create-schema-set-and-friendly-sql) | splink | `database_api.py:84` |
| [Text Functions (similarity)](#63-text-functions-string-similarity-family) | splink | `dialects.py:270–295` |
| [List Functions & 1-based Indexing](#64-list-functions-and-1-based-indexing) | splink | `dialects.py:296–303` |
| [Unnesting](#65-unnesting) | splink | `dialects.py:385–392` |
| [Metadata Functions (duckdb_extensions)](#66-metadata-functions-duckdb_extensions) | ibis | `duckdb/__init__.py:471–504` |
| [DESCRIBE (meta guides)](#67-describe-on-arbitrary-queries) | evidence | `datasources/duckdb/index.cjs:200–207` |
| [Configuration & Pragmas; performance tuning](#71-configuration-pragmas-and-performance-tuning) | smallpond | `task.py:1102–1120` |
| [Profiling](#72-profiling) | smallpond | `task.py:1150–1163` |
| [Extensions: LOAD / INSTALL](#73-extensions-install-load-force-install-and-unsigned-extensions) | tad / ibis / rill | `reltab-duckdb.ts:268`; `__init__.py:488–504`; `duckdbsql.go:53–58` |
| [Extensions: allow_unsigned_extensions](#73-extensions-install-load-force-install-and-unsigned-extensions) | smallpond | `task.py:1774` |
| [Secrets Manager; S3 / GCS / Azure / HTTP secrets](#74-secrets-manager-s3-gcs-azure-and-http-secrets) | rill | `model_executor_self.go:630–760` |
| [Legacy S3 authentication (SET s3_…)](#75-legacy-s3-authentication-with-set-variables) | tad | `s3utils.ts:16–28` |
| [MotherDuck extension](#76-motherduck-extension) | rill | `model_executor_motherduck_self.go:99` |
| [Securing DuckDB (enable_external_access)](#77-securing-duckdb) | rill | `duckdbsql.go:58–60` |
| [Out-of-Memory troubleshooting](#78-out-of-memory-handling) | smallpond | `task.py:1159–1160` |

---

## 4. Data import and export

### 4.1 CSV Auto Detection — three points on the sniffer spectrum

**Docs page:** [Data Import → CSV → Auto Detection](https://duckdb.org/docs/current/data/csv/auto_detection)

The page explains that `read_csv` auto-detects dialect, types and headers by sampling the file, that any detected option can be overridden, and that `sample_size = -1` forces the sniffer to scan the whole file. Three projects illustrate the full spectrum, from fully trusting the sniffer to bypassing it entirely.

**Code:** splink — [`splink/internals/duckdb/database_api.py#L84`](https://github.com/moj-analytical-services/splink/blob/1abd964/splink/internals/duckdb/database_api.py#L84)

```python
self._con.execute(f"CREATE TABLE {tn} AS FROM read_csv_auto('{path}')")
```

The happy path: a record-linkage library ingesting arbitrary user-supplied CSVs with zero options, relying entirely on dialect, type and header sniffing. (The `FROM`-first syntax also illustrates the Friendly SQL page; see [6.2](#62-create-schema-set-and-friendly-sql).)

**Code:** tad — [`packages/reltab-duckdb/src/csvimport.ts#L64-L95`](https://github.com/antonycourtney/tad/blob/272ffa0/packages/reltab-duckdb/src/csvimport.ts#L64-L95)

```typescript
const query = `CREATE OR REPLACE TABLE ${tableName} AS SELECT * FROM read_csv_auto('${filePath}')`;
try {
  const resRows = await dbConn.all(query);
} catch (err) {
  console.log("retrying with SAMPLE_SIZE=-1:");
  const noSampleQuery = `CREATE OR REPLACE TABLE ${tableName} AS SELECT * FROM read_csv_auto('${filePath}', sample_size=-1)`;
  const resRows = await dbConn.all(noSampleQuery);
}
```

The failure path, operationalised as a pattern: attempt the fast sampled sniff first, and only pay the full-file scan when sampled detection throws a conversion error. This is exactly the remediation the [Reading Faulty CSV Files](https://duckdb.org/docs/current/data/csv/reading_faulty_csv_files) page and the [CSV Tips](https://duckdb.org/docs/current/data/csv/tips) page recommend ("Set the sample size to a larger value … e.g., `sample_size=-1`"), embedded in a desktop tool that must open any file a user drops on it.

**Code:** smallpond — [`smallpond/logical/dataset.py#L478-L482`](https://github.com/deepseek-ai/smallpond/blob/52ecc5e/smallpond/logical/dataset.py#L478-L482)

```python
f"( select {self._column_str} from read_csv([ {self._resolved_path_str} ], delim='{self.delim}', columns={{ {schema_str} }}, header={self.header}, "
f"{max_line_size_str} parallel={self.parallel}, union_by_name={self.union_by_name}) )"
```

The bypass path: a distributed pipeline where the schema is declared up front (the class requires an ordered schema dict), so it passes `delim`, `columns` and `header` explicitly and skips sniffing for determinism across many partitioned files. Also demonstrates the list-of-files form and `union_by_name`, linking forward to the [Multiple Files](https://duckdb.org/docs/current/data/multiple_files/overview) / [Combining Schemas](https://duckdb.org/docs/current/data/multiple_files/combining_schemas) pages.

### 4.2 Loading JSON

**Docs page:** [Data Import → JSON → Loading JSON](https://duckdb.org/docs/current/data/json/loading_json)

**Code:** smallpond — [`smallpond/logical/dataset.py#L531-L535`](https://github.com/deepseek-ai/smallpond/blob/52ecc5e/smallpond/logical/dataset.py#L531-L535)

```python
f"( select {self._column_str} from read_json([ {self._resolved_path_str} ], format='{self.format}', columns={{ {schema_str} }}, "
f"maximum_object_size={self.max_object_size}, union_by_name={self.union_by_name}) )"
```

A production `read_json` call exercising three documented parameters at once: `format` (the class defaults to `'newline_delimited'`, i.e., NDJSON), an explicit `columns` schema, and `maximum_object_size` raised to 1 GB for large records — a realistic illustration of why that limit parameter exists.

**Code:** ibis — [`ibis/backends/duckdb/__init__.py#L544-L610`](https://github.com/ibis-project/ibis/blob/799a2a9/ibis/backends/duckdb/__init__.py#L544-L610)

ibis' `read_json` method forwards keyword arguments straight to DuckDB's `read_json_auto` and registers the result as a view — an example of a library exposing the docs' function signature one-to-one to its own users.

### 4.3 Parquet Overview and Multiple Files

**Docs pages:** [Data Import → Parquet → Overview](https://duckdb.org/docs/current/data/parquet/overview) and [Multiple Files → Overview](https://duckdb.org/docs/current/data/multiple_files/overview)

**Code:** smallpond — [`smallpond/logical/dataset.py#L677-L703`](https://github.com/deepseek-ai/smallpond/blob/52ecc5e/smallpond/logical/dataset.py#L677-L703)

```python
from read_parquet('{path}' {extra_parameters}, file_row_number=true)
...
# NOTE: prefer:     read_parquet([path1, path2, ...])
#       instead of: read_parquet(path1) union all read_parquet(path2) union all ...
# XXX: duckdb uses the first file as the estimated cardinality of `read_parquet`
```

Illustrates the documented `file_row_number` generated column and the list-of-files calling convention. The in-code comments are a practitioner footnote the docs could quote: the list form is preferred over `UNION ALL` because of how cardinality is estimated.

### 4.4 COPY … TO, Writing JSON, and Partitioned Writes

**Docs pages:** [SQL → Statements → COPY](https://duckdb.org/docs/current/sql/statements/copy), [Data Import → JSON → Writing JSON](https://duckdb.org/docs/current/data/json/writing_json), [Partitioning → Partitioned Writes](https://duckdb.org/docs/current/data/partitioning/partitioned_writes)

**Code:** rill — [`runtime/drivers/duckdb/model_executor_self_file.go#L150-L154`](https://github.com/rilldata/rill/blob/04d594f/runtime/drivers/duckdb/model_executor_self_file.go#L150-L154)

```go
return fmt.Sprintf("COPY (%s\n) TO '%s' (FORMAT PARQUET)", qry, path), nil
return fmt.Sprintf("COPY (%s\n) TO '%s' (FORMAT CSV, HEADER true, DATEFORMAT '%%x', TIMESTAMPFORMAT '%%c')", qry, path), nil
return fmt.Sprintf("COPY (%s\n) TO '%s' (FORMAT JSON)", qry, path), nil
```

One switch statement covering three documented `COPY` targets — Parquet, CSV (with `HEADER`, `DATEFORMAT` and `TIMESTAMPFORMAT` options), and JSON — used to export BI model results to files.

**Code:** ibis — [`ibis/backends/duckdb/__init__.py#L1480-L1536`](https://github.com/ibis-project/ibis/blob/799a2a9/ibis/backends/duckdb/__init__.py#L1480-L1536) (`to_parquet`)

```python
args = ["FORMAT 'parquet'", *(f"{k.upper()} {v!r}" for k, v in kwargs.items())]
copy_cmd = f"COPY ({query}) TO {str(path)!r} ({', '.join(args)})"
# docstring examples:
# >>> con.to_parquet(penguins, tempfile.mkdtemp(), partition_by="year")
# >>> con.to_parquet(penguins, tempfile.mkdtemp(), partition_by=("year", "island"))
```

ibis compiles any expression into `COPY (…) TO` with pass-through writer options, and its own docstring demonstrates hive-partitioned output via `partition_by` — a direct real-world companion to the Partitioned Writes page (the docstring even links to the DuckDB Parquet docs).

---

## 5. Client APIs

### 5.1 Connect Overview: in-memory vs. persistent databases

**Docs page:** [Connect → Overview](https://duckdb.org/docs/current/connect/overview)

**Code:** splink — [`splink/internals/duckdb/database_api.py#L33-L44`](https://github.com/moj-analytical-services/splink/blob/1abd964/splink/internals/duckdb/database_api.py#L33-L44) and [`duckdb_helpers.py#L9-L40`](https://github.com/moj-analytical-services/splink/blob/1abd964/splink/internals/duckdb/duckdb_helpers/duckdb_helpers.py#L9-L40)

```python
if isinstance(connection, duckdb.DuckDBPyConnection):
    con = connection
elif con_lower == ":memory:":
    con = duckdb.connect(database=connection)
elif con_lower == ":temporary:":
    con = create_temporary_duckdb_connection(self)
else:
    con = duckdb.connect(database=connection)
```

A user-facing API built directly on the docs' connection model: pass an existing connection, `':memory:'`, or a file path. The companion helper validates strings and even nudges users toward the documented `.db` / `.duckdb` file-suffix convention — evidence of the docs' conventions shaping downstream API design.

### 5.2 Python client: connecting with a config dictionary

**Docs pages:** [Clients → Python → Overview](https://duckdb.org/docs/current/clients/python/overview) and [Configuration → Overview](https://duckdb.org/docs/current/configuration/overview)

**Code:** smallpond — [`smallpond/execution/task.py#L1774`](https://github.com/deepseek-ai/smallpond/blob/52ecc5e/smallpond/execution/task.py#L1774) (also lines 2047, 2300, 2627)

```python
conn = duckdb.connect(database=":memory:", config={"allow_unsigned_extensions": "true"})
```

Shows the documented `config=` dictionary form of `duckdb.connect` in a framework that opens fresh in-memory connections per task. The specific option also illustrates the extensions [Advanced Installation Methods](https://duckdb.org/docs/current/extensions/advanced_installation_methods) page (unsigned extensions); see [7.3](#73-extensions-install-load-force-install-and-unsigned-extensions).

### 5.3 Python client: Data Ingestion via register()

**Docs page:** [Clients → Python → Data Ingestion](https://duckdb.org/docs/current/clients/python/data_ingestion)

**Code:** splink — [`splink/internals/duckdb/database_api.py#L58-L75`](https://github.com/moj-analytical-services/splink/blob/1abd964/splink/internals/duckdb/database_api.py#L58-L75)

```python
def delete_table_from_database(self, name: str) -> None:
    # If the table is in fact a pandas dataframe that's been registered using
    # duckdb con.register() then DROP TABLE will fail with
    # CatalogException ... use DROP VIEW instead
    ...

input = to_pyarrow_if_list_tuple_or_dict(input)
self._con.register(table_name, input)
```

Registers pandas/Arrow objects as queryable tables, exactly as the ingestion docs describe — and the surrounding comment documents the practical consequence the docs explain conceptually: registered DataFrames are views, so `DROP TABLE` raises a `CatalogException` and `DROP VIEW` is needed. A ready-made "gotcha" illustration.

### 5.4 Python client: Function API (user-defined functions)

**Docs page:** [Clients → Python → Function API](https://duckdb.org/docs/current/clients/python/function)

**Code:** smallpond — [`smallpond/logical/udf.py#L181-L192`](https://github.com/deepseek-ai/smallpond/blob/52ecc5e/smallpond/logical/udf.py#L181-L192)

```python
conn.create_function(
    self.name,
    self.func,
    duckdb_args,
    self.return_type.to_duckdb_type(),
    type=("arrow" if self.use_arrow_type else "native"),
)
```

A wrapper that lets users declare Python UDFs which are then bound to every worker connection with `create_function` — including the docs' native-versus-Arrow UDF distinction surfaced as a per-function flag. Demonstrates why per-connection binding matters in multi-connection systems.

### 5.5 Python client: Conversion — result sets to Arrow

**Docs pages:** [Clients → Python → Conversion](https://duckdb.org/docs/current/clients/python/conversion) and [Guides → Python → Export to Arrow](https://duckdb.org/docs/current/guides/python/export_arrow)

**Code:** smallpond — [`smallpond/logical/dataset.py#L279-L323`](https://github.com/deepseek-ai/smallpond/blob/52ecc5e/smallpond/logical/dataset.py#L279-L323)

```python
return conn.sql(sql_query).fetch_arrow_table()
...
return conn.sql(sql_query).fetch_arrow_reader(batch_size)
```

Both documented Arrow export paths in one file: `fetch_arrow_table()` for materialised results and `fetch_arrow_reader(batch_size)` for streaming record batches — the latter being the pattern the docs recommend for larger-than-memory result handling.

### 5.6 Node.js (Neo) client

**Docs page:** [Clients → Node.js (Neo) → Overview](https://duckdb.org/docs/current/clients/node_neo/overview)

**Code:** evidence — [`packages/datasources/duckdb/index.cjs#L147-L217`](https://github.com/evidence-dev/evidence/blob/bdf2ce1/packages/datasources/duckdb/index.cjs#L147-L217)

```javascript
const { DuckDBInstance } = require('@duckdb/node-api');

const db = await DuckDBInstance.create(filename, {
    access_mode: mode,            // 'READ_ONLY' for file DBs
    custom_user_agent: 'evidence-dev'
});
const conn = await db.connect();

const countReader = await conn.runAndReadAll(count_query);
reader = await conn.streamAndRead(mainStatement);
```

A complete Neo-client lifecycle: `DuckDBInstance.create` with configuration (including `access_mode: READ_ONLY`, illustrating the read-only connection concept, and `custom_user_agent`, illustrating the Operations Manual's [User Agents](https://duckdb.org/docs/current/operations_manual/user_agents) page), then `runAndReadAll` for small metadata queries and `streamAndRead` for batch-streaming large results — the exact API pairing the Neo docs introduce.

### 5.7 Go client

**Docs page:** [Clients → Go](https://duckdb.org/docs/current/clients/go)

**Code:** rill — [`runtime/pkg/duckdbsql/duckdbsql.go#L50-L69`](https://github.com/rilldata/rill/blob/04d594f/runtime/pkg/duckdbsql/duckdbsql.go#L50-L69)

```go
// Using NewConnector since DuckDB requires extensions to be loaded separately on each connection
connector, err := duckdb.NewConnector("", func(conn driver.ExecerContext) error {
    _, err := conn.ExecContext(context.Background(), "INSTALL 'json'; LOAD 'json';", nil)
    ...
})
db = databasesql.OpenDB(connector)
db.SetMaxOpenConns(1)
```

The Go docs' `NewConnector`-with-boot-queries pattern in production, with an explanatory comment on why it exists (extensions load per connection). [`rduckdb/db.go#L899`](https://github.com/rilldata/rill/blob/04d594f/runtime/pkg/rduckdb/db.go#L899) additionally shows `NewConnector` with a DSN carrying settings as query parameters — the documented `dsn?key=value` configuration style.

### 5.8 Wasm: Instantiation

**Docs page:** [Clients → Wasm → Instantiation](https://duckdb.org/docs/current/clients/wasm/instantiation)

**Code:** evidence — [`packages/lib/universal-sql/src/client-duckdb/browser.js#L38-L70`](https://github.com/evidence-dev/evidence/blob/bdf2ce1/packages/lib/universal-sql/src/client-duckdb/browser.js#L38-L70)

```javascript
const useEh = await getPlatformFeatures().then((x) => x.wasmExceptions);

const DUCKDB_CONFIG = useEh
  ? {
        mainModule: (await import('@duckdb/duckdb-wasm/dist/duckdb-eh.wasm?url')).default,
        mainWorker: (await import('@duckdb/duckdb-wasm/dist/duckdb-browser-eh.worker.js?worker')).default
    }
  : {
        mainModule: (await import('@duckdb/duckdb-wasm/dist/duckdb-mvp.wasm?url')).default,
        mainWorker: (await import('@duckdb/duckdb-wasm/dist/duckdb-browser-mvp.worker.js?worker')).default
    };

const _db = new AsyncDuckDB(logger, worker);
await _db.instantiate(DUCKDB_CONFIG.mainModule);

await db.open({
    query: {
        castBigIntToDouble: true,
        castTimestampToDate: true,
        castDecimalToDouble: true,
        castDurationToTime64: true
    }
});
```

Nearly a line-for-line real-world version of the instantiation page: feature-detecting between the `eh` and `mvp` bundles, wiring up the worker and `AsyncDuckDB`, then opening with the documented query-config cast options. The sibling [`node.js#L45-L60`](https://github.com/evidence-dev/evidence/blob/bdf2ce1/packages/lib/universal-sql/src/client-duckdb/node.js#L45-L60) shows the same flow with the Node worker bundles.

### 5.9 Wasm: Data Ingestion

**Docs page:** [Clients → Wasm → Data Ingestion](https://duckdb.org/docs/current/clients/wasm/data_ingestion)

**Code:** evidence — [`packages/lib/universal-sql/src/client-duckdb/browser.js#L135-L138`](https://github.com/evidence-dev/evidence/blob/bdf2ce1/packages/lib/universal-sql/src/client-duckdb/browser.js#L135-L138)

```javascript
await db.registerFileURL(file_name, addBasePath(path), DuckDBDataProtocol.HTTP, false);
await connection.query(
    `CREATE OR REPLACE VIEW "${source}"."${table}" AS (SELECT * FROM read_parquet('${file_name}'));`
);
```

The documented `registerFileURL` + `DuckDBDataProtocol.HTTP` pattern for exposing remote Parquet files to the Wasm virtual filesystem, immediately consumed with `read_parquet` behind a view — how a real BI framework loads its pre-built Parquet artifacts into the browser.

---

## 6. SQL statements, dialect, and functions

### 6.1 ATTACH and DETACH; PostgreSQL and MySQL extensions

**Docs pages:** [SQL → Statements → ATTACH and DETACH](https://duckdb.org/docs/current/sql/statements/attach), [Core Extensions → PostgreSQL](https://duckdb.org/docs/current/core_extensions/postgres/overview), [Core Extensions → MySQL](https://duckdb.org/docs/current/core_extensions/mysql)

**Code:** ibis — [`ibis/backends/duckdb/__init__.py#L1194-L1215`](https://github.com/ibis-project/ibis/blob/799a2a9/ibis/backends/duckdb/__init__.py#L1194-L1215)

```python
code = f"ATTACH '{path}'"
if name is not None:
    code += f" AS {name}"
if read_only:
    code += " (READ_ONLY)"
self.con.execute(code).fetchall()
...
self.con.execute(f"DETACH {name}").fetchall()
```

ibis exposes `attach()`/`detach()` methods that assemble exactly the documented `ATTACH` grammar, including the `AS` alias and `(READ_ONLY)` option.

**Code:** rill — [`runtime/drivers/duckdb/model_executor_sqlstore_self.go#L110-L125`](https://github.com/rilldata/rill/blob/04d594f/runtime/drivers/duckdb/model_executor_sqlstore_self.go#L110-L125)

```go
m.PreExec = fmt.Sprintf("INSTALL 'MYSQL'; LOAD 'MYSQL'; ATTACH %s AS %s (TYPE mysql, READ_ONLY)", safeSQLString(dsn), safeDBName)
m.PreExec = fmt.Sprintf("INSTALL 'POSTGRES'; LOAD 'POSTGRES'; ATTACH %s AS %s (TYPE postgres, READ_ONLY)", safeSQLString(dsn), safeDBName)
```

The database-extension variant: install-load-attach one-liners for the MySQL and PostgreSQL scanners, mirroring the quick-start snippets on both extension pages (with `READ_ONLY` as the safety default). [`model_executor_self.go#L391`](https://github.com/rilldata/rill/blob/04d594f/runtime/drivers/duckdb/model_executor_self.go#L391) shows the plain DuckDB-file case: `ATTACH … AS … (READ_ONLY)`.

### 6.2 CREATE SCHEMA, SET, and Friendly SQL

**Docs pages:** [SQL → Statements → CREATE SCHEMA](https://duckdb.org/docs/current/sql/statements/create_schema) and [SQL → Dialect → Friendly SQL](https://duckdb.org/docs/current/sql/dialect/friendly_sql)

**Code:** splink — [`splink/internals/duckdb/database_api.py#L46-L52`](https://github.com/moj-analytical-services/splink/blob/1abd964/splink/internals/duckdb/database_api.py#L46-L52) and [line 84](https://github.com/moj-analytical-services/splink/blob/1abd964/splink/internals/duckdb/database_api.py#L84)

```sql
CREATE SCHEMA IF NOT EXISTS {output_schema};
SET schema '{output_schema}';
-- ...
CREATE TABLE {tn} AS FROM read_csv_auto('{path}')
```

Schema management straight from the CREATE SCHEMA page (`IF NOT EXISTS` plus `SET schema` for the search path), and — in the same file — a `FROM`-first statement with no `SELECT` clause, one of the flagship conveniences on the Friendly SQL page, used unremarked in production code.

### 6.3 Text Functions: string-similarity family

**Docs page:** [SQL → Functions → Text Functions](https://duckdb.org/docs/current/sql/functions/text)

**Code:** splink — [`splink/internals/dialects.py#L270-L295`](https://github.com/moj-analytical-services/splink/blob/1abd964/splink/internals/dialects.py#L270-L295) (`DuckDBDialect`)

```python
class DuckDBDialect(SplinkDialect):
    def levenshtein_function_name(self):         return "levenshtein"
    def damerau_levenshtein_function_name(self): return "damerau_levenshtein"
    def jaro_function_name(self):                return "jaro_similarity"
    def jaro_winkler_function_name(self):        return "jaro_winkler_similarity"
    def jaccard_function_name(self):             return "jaccard"
```

Splink's entire fuzzy-matching engine is parameterised over SQL dialects, and its DuckDB dialect maps one-to-one onto five similarity functions from the Text Functions page. A strong illustration that these functions are not curiosities: they are the computational core of a widely used probabilistic record-linkage library (comparison levels generate predicates like `jaro_winkler_similarity(name_l, name_r) >= threshold`).

### 6.4 List Functions and 1-based indexing

**Docs pages:** [SQL → Functions → List Functions](https://duckdb.org/docs/current/sql/functions/list) and [SQL → Dialect → Indexing](https://duckdb.org/docs/current/sql/dialect/indexing)

**Code:** splink — [`splink/internals/dialects.py#L296-L303`](https://github.com/moj-analytical-services/splink/blob/1abd964/splink/internals/dialects.py#L296-L303)

```python
def array_max_function_name(self):       return "list_max"
def array_min_function_name(self):       return "list_min"
def array_transform_function_name(self): return "list_transform"
def array_first_index(self):             return 1
```

The dialect layer maps splink's abstract array operations to DuckDB's `list_*` functions — and hard-codes `array_first_index = 1`, a compact real-world witness for the Indexing dialect page's statement that DuckDB lists are 1-based (other dialects in the same file return 0).

### 6.5 Unnesting

**Docs page:** [SQL → Query Syntax → Unnesting](https://duckdb.org/docs/current/sql/query_syntax/unnest)

**Code:** splink — [`splink/internals/dialects.py#L385-L392`](https://github.com/moj-analytical-services/splink/blob/1abd964/splink/internals/dialects.py#L385-L392) (`explode_arrays_sql`)

```python
[f"unnest({column_to_explode}) as {column_to_explode}"]
```

Splink's "exploding blocking rules" feature turns array columns into one row per element with `unnest()` — a concrete, high-value use (blocking on multi-valued fields such as alternative names) for the Unnesting page.

### 6.6 Metadata Functions: duckdb_extensions()

**Docs page:** [SQL → Meta Queries → Metadata Functions](https://duckdb.org/docs/current/sql/meta/duckdb_table_functions)

**Code:** ibis — [`ibis/backends/duckdb/__init__.py#L471-L504`](https://github.com/ibis-project/ibis/blob/799a2a9/ibis/backends/duckdb/__init__.py#L471-L504) (`_load_extensions`)

```python
query = (
    sg.select(
        f.anon.unnest(
            f.list_intersect(
                f.list_append(C.aliases, C.extension_name),
                f.list_value(*extensions),
            )
        ),
        C.installed,
        C.loaded,
    )
    .from_(f.duckdb_extensions())
    .where(sg.not_(C.installed & C.loaded))
)
# then: FORCE INSTALL '<ext>' / INSTALL '<ext>' and LOAD '<ext>' as needed
```

ibis queries the `duckdb_extensions()` table function — including its `installed`, `loaded` and `aliases` columns — to decide which `INSTALL`/`LOAD` statements are actually needed, then issues them (with optional `FORCE INSTALL`). One method illustrates the metadata-functions page, the [LOAD/INSTALL](https://duckdb.org/docs/current/sql/statements/load_and_install) statement page, and the extension-alias behaviour at once.

### 6.7 DESCRIBE on arbitrary queries

**Docs pages:** [SQL → Statements → DESCRIBE](https://duckdb.org/docs/current/sql/statements/describe) and [Guides → Meta Queries → Describe Table](https://duckdb.org/docs/current/guides/meta/describe)

**Code:** evidence — [`packages/datasources/duckdb/index.cjs#L200-L207`](https://github.com/evidence-dev/evidence/blob/bdf2ce1/packages/datasources/duckdb/index.cjs#L200-L207)

```javascript
const column_query = mainStatement ? `DESCRIBE ${cleanQuery(mainStatement)}` : null;
const colReader = await conn.runAndReadAll(column_query);
column_types = duckdbDescribeToEvidenceType(describeRows);
```

The docs note that `DESCRIBE` works on queries, not just tables; evidence relies on exactly that to infer result-set column types for its rendering layer before streaming the actual data.

---

## 7. Configuration, extensions, and operations

### 7.1 Configuration, Pragmas, and performance tuning

**Docs pages:** [Configuration → Overview](https://duckdb.org/docs/current/configuration/overview), [Configuration → Pragmas](https://duckdb.org/docs/current/configuration/pragmas), and [Guides → Performance → How to Tune Workloads](https://duckdb.org/docs/current/guides/performance/how_to_tune_workloads)

**Code:** smallpond — [`smallpond/execution/task.py#L1102-L1120`](https://github.com/deepseek-ai/smallpond/blob/52ecc5e/smallpond/execution/task.py#L1102-L1120) (`prepare_connection`)

```sql
SET threads TO {effective_cpu_count};
SET memory_limit='{effective_memory_size // MB}MB';
SET temp_directory='{...}';
SET enable_object_cache=true;
SET arrow_large_buffer_size=true;
SET preserve_insertion_order=false;
SET max_expression_depth=10000;
```

Seven documented settings in one connection-preparation block, sized per task from the scheduler's CPU/memory allocation. It concretises the tuning guide's core advice — cap `threads` and `memory_limit` per workload, point `temp_directory` at fast storage for spilling, and disable insertion-order preservation to cut memory usage — inside a system that runs many DuckDB instances side by side.

### 7.2 Profiling

**Docs pages:** [SQL → Statements → Profiling](https://duckdb.org/docs/current/sql/statements/profiling) and [Development → Profiling](https://duckdb.org/docs/current/dev/profiling)

**Code:** smallpond — [`smallpond/execution/task.py#L1150-L1163`](https://github.com/deepseek-ai/smallpond/blob/52ecc5e/smallpond/execution/task.py#L1150-L1163)

```python
query_output = conn.sql("SET enable_profiling='json';" if enable_profiling else "RESET enable_profiling;")
query_output = conn.sql(query_statement)
```

Programmatic use of `SET enable_profiling='json'` / `RESET enable_profiling` around individual queries; the code that follows walks the JSON profile tree summing per-operator metrics — a real consumer of the documented JSON profiling output format.

### 7.3 Extensions: INSTALL, LOAD, FORCE INSTALL, and unsigned extensions

**Docs pages:** [SQL → Statements → LOAD / INSTALL](https://duckdb.org/docs/current/sql/statements/load_and_install) and [Extensions → Advanced Installation Methods](https://duckdb.org/docs/current/extensions/advanced_installation_methods)

**Code:** tad — [`packages/reltab-duckdb/src/reltab-duckdb.ts#L268`](https://github.com/antonycourtney/tad/blob/272ffa0/packages/reltab-duckdb/src/reltab-duckdb.ts#L268)

```typescript
const ret = await db.exec(`INSTALL 'httpfs'; LOAD 'httpfs'`);
```

The canonical two-statement install-and-load, enabling remote file access in a desktop app. ibis ([6.6](#66-metadata-functions-duckdb_extensions)) adds the `FORCE INSTALL` variant and conditional loading; rill's Go boot query ([5.7](#57-go-client)) shows `INSTALL 'json'; LOAD 'json'` per connection. smallpond's connect config `allow_unsigned_extensions: 'true'` ([`task.py#L1774`](https://github.com/deepseek-ai/smallpond/blob/52ecc5e/smallpond/execution/task.py#L1774)) illustrates the advanced-installation page's unsigned-extension switch, which it uses to load a custom filesystem extension.

### 7.4 Secrets Manager: S3, GCS, Azure, and HTTP secrets

**Docs pages:** [Configuration → Secrets Manager](https://duckdb.org/docs/current/configuration/secrets_manager) and [Core Extensions → httpfs → S3 API Support](https://duckdb.org/docs/current/core_extensions/httpfs/s3api)

**Code:** rill — [`runtime/drivers/duckdb/model_executor_self.go#L630-L760`](https://github.com/rilldata/rill/blob/04d594f/runtime/drivers/duckdb/model_executor_self.go#L630-L760)

```go
sb.WriteString("CREATE OR REPLACE TEMPORARY SECRET ")
sb.WriteString(safeSecretName)
sb.WriteString(" (TYPE S3")
fmt.Fprintf(&sb, ", KEY_ID %s, SECRET %s", safeSQLString(s3Config.AccessKeyID), safeSQLString(s3Config.SecretAccessKey))
sb.WriteString(", PROVIDER CREDENTIAL_CHAIN, VALIDATION 'none'")
fmt.Fprintf(&sb, ", SESSION_TOKEN %s", safeSQLString(s3Config.SessionToken))
sb.WriteString(", ENDPOINT "); sb.WriteString(", REGION ")
// analogous builders for TYPE GCS, TYPE AZURE (CONNECTION_STRING, ACCOUNT_NAME),
// and TYPE HTTP (EXTRA_HTTP_HEADERS MAP { ... })
```

Possibly the single richest real-world illustration of the Secrets Manager: temporary secrets built for four provider types, exercising `KEY_ID`/`SECRET`, `SESSION_TOKEN`, `PROVIDER CREDENTIAL_CHAIN`, `ENDPOINT`, `REGION`, `USE_SSL`, `SCOPE` (via `writeScope` over path prefixes), Azure `CONNECTION_STRING`/`ACCOUNT_NAME`, and HTTP `EXTRA_HTTP_HEADERS`. The comments also record operational nuance the docs can point to (e.g., resolving the S3 region manually; assuming an IAM role by fetching temporary credentials and inlining them).

### 7.5 Legacy S3 authentication with SET variables

**Docs page:** [Core Extensions → httpfs → Legacy Authentication Scheme for S3 API](https://duckdb.org/docs/current/core_extensions/httpfs/s3api_legacy_authentication)

**Code:** tad — [`packages/reltab-duckdb/src/s3utils.ts#L16-L28`](https://github.com/antonycourtney/tad/blob/272ffa0/packages/reltab-duckdb/src/s3utils.ts#L16-L28)

```typescript
await dbConn.exec(`SET s3_region='${s3_region}'`);
await dbConn.exec(`SET s3_access_key_id='${aws_access_key_id}'`);
await dbConn.exec(`SET s3_secret_access_key='${process.env.AWS_SECRET_ACCESS_KEY}'`);
```

The pre-secrets configuration style (`SET s3_region` / `s3_access_key_id` / `s3_secret_access_key`), populated from AWS environment variables. Paired with rill's secret-based approach above, the two repos illustrate the docs' old and new S3 authentication pages side by side — useful for a migration note.

### 7.6 MotherDuck extension

**Docs page:** [Core Extensions → MotherDuck](https://duckdb.org/docs/current/core_extensions/motherduck)

**Code:** rill — [`runtime/drivers/duckdb/model_executor_motherduck_self.go#L99`](https://github.com/rilldata/rill/blob/04d594f/runtime/drivers/duckdb/model_executor_motherduck_self.go#L99)

```go
InitQueries: fmt.Sprintf("INSTALL 'motherduck'; LOAD 'motherduck'; SET motherduck_token=%s; ATTACH %s;", safeSQLString(token), safeSQLString(dsn)),
```

The full documented connection sequence for MotherDuck — install and load the extension, set `motherduck_token`, then `ATTACH` the `md:` DSN — packaged as per-connection init queries.

### 7.7 Securing DuckDB

**Docs page:** [Operations Manual → Securing DuckDB → Overview](https://duckdb.org/docs/current/operations_manual/securing_duckdb/overview)

**Code:** rill — [`runtime/pkg/duckdbsql/duckdbsql.go#L58-L60`](https://github.com/rilldata/rill/blob/04d594f/runtime/pkg/duckdbsql/duckdbsql.go#L58-L60)

```go
// Lock it down
_, err = conn.ExecContext(context.Background(), "SET enable_external_access=false", nil)
```

The security page's headline recommendation — disabling external access on a DuckDB instance that only needs to parse/inspect SQL — applied verbatim, comment included. A crisp illustration for the "Embedding DuckDB" / hardening guidance.

### 7.8 Out-of-memory handling

**Docs pages:** [Guides → Performance → Out-of-Memory Issues](https://duckdb.org/docs/current/guides/performance/oom) and [Guides → Troubleshooting → Out of Memory Errors](https://duckdb.org/docs/current/guides/troubleshooting/oom_errors)

**Code:** smallpond — [`smallpond/execution/task.py#L1159-L1160`](https://github.com/deepseek-ai/smallpond/blob/52ecc5e/smallpond/execution/task.py#L1159-L1160)

```python
except duckdb.OutOfMemoryException as ex:
    raise OutOfMemory(f"{self.key} failed with OOM error") from ex
```

The Python client surfaces OOM as a typed `duckdb.OutOfMemoryException`; smallpond catches it specifically so its scheduler can retry the task with a larger memory allocation — a real recovery strategy that complements the OOM guide's prevention advice (and its `memory_limit` / `temp_directory` settings shown in [7.1](#71-configuration-pragmas-and-performance-tuning)).

---

## 8. Notes on incorporating these references

**Pin to commits, not branches.** All links above use commit SHAs (see Section 2), so the cited line numbers are stable permalinks. If the docs prefer to track the projects' `main` branches, the references should be re-verified on each docs release.

**Prefer references that add information.** The strongest candidates are those where the code encodes an operational lesson beyond the docs' synthetic examples: tad's `sample_size=-1` retry (4.1), smallpond's cardinality-estimation comment on `read_parquet` lists (4.3), splink's `DROP TABLE`-vs-`DROP VIEW` comment on registered DataFrames (5.3), rill's per-connection extension-loading comment (5.7), and rill's region-resolution and role-assumption workarounds in its secret builders (7.4).

**Coverage gaps worth pursuing next.** Within this survey no high-quality public illustrations were pinned down for the Appender API, prepared statements, window functions / `QUALIFY`, `PIVOT`/`UNPIVOT`, or the spatial extension; likely candidates include the Java/JDBC and C ecosystems for the Appender, and GIS-oriented projects such as [opengeos/GeoLibre](https://github.com/opengeos/GeoLibre) for spatial. These were out of scope for the six repositories examined but the same method applies.