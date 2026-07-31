---
type: archive
url: https://docs.databricks.com/aws/en/uc-semantics/metric-views/yaml-reference
source_note: "[[2026-07-31 Databricks - Metric View YAML Reference]]"
archived: 2026-07-31
---

Metric view YAML syntax reference | Databricks on AWS

On this page

Last updated on Jul 24, 2026

Metric view definitions use standard YAML syntax to declare the source, joins, fields, measures, filters, window measures, and materialization. The following sections document the complete grammar for each.

For minimum runtime and YAML specification version requirements for each feature, see Metric view feature availability.

See YAML Specification 1.2.2 documentation to learn more about YAML specifications.

## Edit YAML in the metric view editor​

You can write and edit the YAML described on this page directly in the metric view editor. In Catalog Explorer, open a metric view and click the`<>` button to edit the definition. To generate YAML from a natural language description instead, open Genie Code from the editor. For the full editor walkthrough, see Create a metric view.

## Top-level YAML fields​

The YAML definition for a metric view includes the following top-level fields:

| Field | Type | Description |
| --- | --- | --- |
| `version` | String | Required. The version of the metric view YAML specification that the definition uses, such as`1.1`. This is the version of the specification format, not a revision number that you assign to your own definition. Use one of the supported specification versions. See YAML specification versions. |
| `comment` | String | Optional. Description of the metric view. |
| `source` | String | Required. The source data for the metric view. Can be any table-like Unity Catalog asset including a metric view or a SQL query. See Source. |
| `parameters` | Array | Optional. Named values that callers pass when they query the metric view as a table-valued function. See Parameters. |
| `filter` | String | Optional. A SQL boolean expression that applies to all queries. See Filter. |
| `joins` | Array | Optional. Star schema and snowflake schema joins. See Joins. |
| `fields` | Array | Conditional. Field definitions including name, expression, and optional semantic metadata. Required if no`measures` are specified. See Fields. The`dimensions` keyword is accepted as a synonym for backward compatibility. |
| `measures` | Array | Conditional. Measure definitions including name, aggregate expression, and optional semantic metadata. Required if no`fields` are specified. See Measures. |
| `materialization` | Object | Optional. Configuration for accelerating queries with materialized views. Includes refresh schedule and materialized view definitions. See Materialization. |

←✕

| Field | Type | Description |
| --- | --- | --- |
| `version` | String | Required. The version of the metric view YAML specification that the definition uses, such as`1.1`. This is the version of the specification format, not a revision number that you assign to your own definition. Use one of the supported specification versions. See YAML specification versions. |
| `comment` | String | Optional. Description of the metric view. |
| `source` | String | Required. The source data for the metric view. Can be any table-like Unity Catalog asset including a metric view or a SQL query. See Source. |
| `parameters` | Array | Optional. Named values that callers pass when they query the metric view as a table-valued function. See Parameters. |
| `filter` | String | Optional. A SQL boolean expression that applies to all queries. See Filter. |
| `joins` | Array | Optional. Star schema and snowflake schema joins. See Joins. |
| `fields` | Array | Conditional. Field definitions including name, expression, and optional semantic metadata. Required if no`measures` are specified. See Fields. The`dimensions` keyword is accepted as a synonym for backward compatibility. |
| `measures` | Array | Conditional. Measure definitions including name, aggregate expression, and optional semantic metadata. Required if no`fields` are specified. See Measures. |
| `materialization` | Object | Optional. Configuration for accelerating queries with materialized views. Includes refresh schedule and materialized view definitions. See Materialization. |

## Source​

The`source` field specifies the data source for the metric view. Supported sources include tables, views, metric views, and SQL queries. Composability applies across metric views. When using a metric view as a source, you can reference its fields and measures in the new metric view. See Composability.

### Table-like asset source​

Reference a table-like asset using its three-part name:

YAML

```yaml
source: catalog.schema.source_table
```

### SQL query source​

To use a SQL query, write the query text directly in the YAML:

YAML

```yaml
source: SELECT * FROM samples.tpch.orders o  LEFT JOIN samples.tpch.customer c  ON o.o_custkey = c.c_custkey
```

note

When using a SQL query as a source with a`JOIN` clause, set primary and foreign key constraints on underlying tables and use the`RELY` option for optimal query performance. For more information, see Declare primary key, foreign key, and unique constraints and Query optimization using primary key and unique constraints.

## Parameters​

The`parameters` block defines named values that callers pass when they query the metric view as a table-valued function. For when and how to use parameters, including querying a parameterized metric view, see Use parameters with metric views.

Each parameter definition includes the following fields:

| Field | Type | Description |
| --- | --- | --- |
| `name` | String | Required. The parameter name. Reference the parameter by this name in field and measure expressions, and pass it as a named argument when you query the metric view. |
| `data_type` | String | Required. The SQL data type of the parameter, such as`double`,`int`,`string`, or`date`. |
| `default` | Varies | Optional. The value used when a caller does not pass the parameter. The default must be castable to`data_type`, and it can't reference another parameter or contain a subquery. If you set a default for one parameter, every parameter that follows it must also have a default. |

←✕

| Field | Type | Description |
| --- | --- | --- |
| `name` | String | Required. The parameter name. Reference the parameter by this name in field and measure expressions, and pass it as a named argument when you query the metric view. |
| `data_type` | String | Required. The SQL data type of the parameter, such as`double`,`int`,`string`, or`date`. |
| `default` | Varies | Optional. The value used when a caller does not pass the parameter. The default must be castable to`data_type`, and it can't reference another parameter or contain a subquery. If you set a default for one parameter, every parameter that follows it must also have a default. |

The following example defines a`discount` parameter and references it in a measure expression:

YAML

```yaml
version: 1.1source: main.default.salesparameters:  - name: discount    data_type: double    default: 0fields:  - name: product    expr: productmeasures:  - name: discountedSales    expr: SUM((1 - discount) * amount)
```

## Filter​

A filter in the YAML definition applies to all queries that reference the metric view. Write filters as SQL boolean expressions.

YAML

```yaml
# Single condition filterfilter: o_orderdate > '2024-01-01'# Multiple conditions with ANDfilter: o_orderdate > '2024-01-01' AND o_orderstatus = 'F'# Multiple conditions with ORfilter: o_orderpriority = '1-URGENT' OR o_orderpriority = '2-HIGH'# Complex filter with IN clausefilter: o_orderstatus IN ('F', 'P') AND o_orderdate >= '2024-01-01'# Filter with NOTfilter: o_orderstatus != 'O' AND o_totalprice > 1000.00# Filter with LIKE pattern matchingfilter: o_comment LIKE '%express%' AND o_orderdate > '2024-01-01'
```

## Joins​

Joins in metric views support both direct joins from a fact table to dimension tables (star schema) and multi-hop joins across normalized dimension tables (snowflake schemas). You can also join to a SQL query using a`SELECT` statement. See Use a SQL query as a source.

note

Joined tables can't include`MAP` type columns. To unpack values from`MAP` type columns, see Explode nested elements from a map or array.

Each join definition includes the following fields:

| Field | Type | Description |
| --- | --- | --- |
| `name` | String | Required. Alias for the joined table or SQL query. Use this alias when referencing columns from the joined table in fields or measures. |
| `source` | String | Required. Three-part name of the table to join. Can also be a SQL query. |
| `on` | String | Conditional. Boolean expression defining the join condition. Required if`using` is not specified. |
| `using` | Array | Conditional. List of column names present in both the parent table and joined table. Required if`on` is not specified. |
| `cardinality` | String | Optional. Defaults to`many_to_one`. The relationship between the source and the joined table. Set to`one_to_many` to aggregate a table that has multiple matching rows per source row as a separate fact source. See One-to-many joins. |
| `joins` | Array | Optional. A list of nested join definitions for snowflake schema modeling. See Metric view feature availability for minimum runtime requirements. |
| `rely` | Map | Optional. Promises about the join that the analyzer can rely on to produce more efficient query plans. See Optimize joins with`rely`. |

←✕

| Field | Type | Description |
| --- | --- | --- |
| `name` | String | Required. Alias for the joined table or SQL query. Use this alias when referencing columns from the joined table in fields or measures. |
| `source` | String | Required. Three-part name of the table to join. Can also be a SQL query. |
| `on` | String | Conditional. Boolean expression defining the join condition. Required if`using` is not specified. |
| `using` | Array | Conditional. List of column names present in both the parent table and joined table. Required if`on` is not specified. |
| `cardinality` | String | Optional. Defaults to`many_to_one`. The relationship between the source and the joined table. Set to`one_to_many` to aggregate a table that has multiple matching rows per source row as a separate fact source. See One-to-many joins. |
| `joins` | Array | Optional. A list of nested join definitions for snowflake schema modeling. See Metric view feature availability for minimum runtime requirements. |
| `rely` | Map | Optional. Promises about the join that the analyzer can rely on to produce more efficient query plans. See Optimize joins with`rely`. |

### Star schema joins​

In a star schema, the`source` is the fact table and joins with one or more dimension tables using a`LEFT OUTER JOIN`. Metric views join the fact and dimension tables needed for the specific query, based on the selected columns.

Specify join columns using either an`ON` clause or a`USING` clause:

- `ON` clause: Uses a boolean expression to define the join condition.
- `USING` clause: Lists columns with the same name in both the parent table and the joined table.

The join must follow a many-to-one relationship. In cases of many-to-many, the first matching row from the joined dimension table is selected.

YAML

```yaml
version: 1.1source: samples.tpch.lineitemjoins:  - name: orders    source: samples.tpch.orders    on: source.l_orderkey = orders.o_orderkey  - name: part    source: samples.tpch.part    on: source.l_partkey = part.p_partkeyfields:  - name: Order Status    expr: orders.o_orderstatus  - name: Part Name    expr: part.p_namemeasures:  - name: Total Revenue    expr: SUM(l_extendedprice * (1 - l_discount))  - name: Line Item Count    expr: COUNT(1)
```

note

The`source` namespace references columns from the metric view's source, while a join's`name` refers to columns from that joined table. For example, in`source.l_orderkey = orders.o_orderkey`,`source` refers to`lineitem` and`orders` refers to the joined table. If no prefix is provided in an`on` clause, the reference defaults to the joined table.

### Snowflake schema joins​

A snowflake schema extends a star schema by normalizing dimension tables and connecting them to subdimensions. This creates a multi-level join structure. See Metric view feature availability for minimum runtime requirements.

To define a snowflake schema, nest`joins` inside a parent join definition:

YAML

```yaml
version: 1.1source: samples.tpch.ordersjoins:  - name: customer    source: samples.tpch.customer    'on': o_custkey = c_custkey    joins:      - name: nation        source: samples.tpch.nation        'on': c_nationkey = n_nationkeyfields:  - name: customer_nation    expr: customer.nation.n_name
```

### One-to-many joins​

The`cardinality` field sets the relationship between the source and a joined table. The default,`many_to_one`, treats the joined table as a dimension lookup. Set`cardinality: one_to_many` to treat the joined table as a fact source that the engine aggregates independently at the source grain, which lets a single source row match multiple rows in the joined table. One-to-many joins require Databricks Runtime 18.1 or above and YAML specification version 1.1. See Metric view feature availability.

The following rules apply to one-to-many joins:

- A one-to-many column can't be used in a`fields` definition, because a field must resolve to a single value per source row.
- A single aggregation function must reference columns from one source. You can apply arithmetic across the results of separate aggregations, such as`count(orders.order_id) / count(*)`.
- All descendants of a one-to-many join must also be`one_to_many`. Top-level sibling joins can mix cardinalities.
- Reference a column in a nested join with its full dot-path through the join names, such as`orders.order_items.item_id`.

note

When a metric view uses a`one_to_many` join, its materializations qualify for exact match only. Rollup match is not available. See Rollup match.

The following example joins`orders` to a`customers` source with`cardinality: one_to_many` so that order measures aggregate without duplicating customer rows:

YAML

```yaml
version: 1.1source: main.sales.customersjoins:  - name: orders    source: main.sales.orders    on: orders.customer_id = source.customer_id    cardinality: one_to_manyfields:  - name: customer_name    expr: customer_namemeasures:  - name: customer_count    expr: count(*)  - name: order_count    expr: count(orders.order_id)  - name: total_order_revenue    expr: sum(orders.amount)
```

For conceptual details and nested and sibling join examples, see Join cardinality.

### Optimize joins with rely​

Use the`rely` field on a join to declare guarantees about the relationship that the query analyzer uses when planning queries. These guarantees allow the engine to plan queries more efficiently and reduce data scanned, especially when fields from the joined table are referenced in filters.

The`rely` map supports the following fields:

| Field | Type | Description |
| --- | --- | --- |
| `at_most_one_match` | Boolean | Optional. Defaults to`false`. When`true`, declares that at most one row in the joined table matches each row in the source (a many-to-one relationship that does not fan out). |

←✕

| Field | Type | Description |
| --- | --- | --- |
| `at_most_one_match` | Boolean | Optional. Defaults to`false`. When`true`, declares that at most one row in the joined table matches each row in the source (a many-to-one relationship that does not fan out). |

warning

Set`at_most_one_match: true` only when the join is many-to-one. This relationship is not validated at runtime. If multiple rows in the joined table match a single source row, measures (such as`SUM` and`COUNT`) return incorrect results.

The following example enables`at_most_one_match` on a many-to-one join from`orders` to`customer`. Queries that filter or group by customer attributes benefit the most:

YAML

```yaml
version: 1.1source: samples.tpch.ordersjoins:  - name: customer    source: samples.tpch.customer    on: source.o_custkey = customer.c_custkey    rely:      at_most_one_match: truefields:  - name: Customer name    expr: customer.c_name  - name: Customer market segment    expr: customer.c_mktsegmentmeasures:  - name: Total revenue    expr: SUM(o_totalprice)
```

## Fields​

note

`fields` and`dimensions` are equivalent keywords in a metric view definition.`fields` is the preferred term and is used throughout this documentation. The Catalog Explorer low-code editor labels these columns Fields, but the YAML it generates uses the`dimensions` keyword. Existing metric views that use`dimensions` continue to work, and both keywords are accepted on new or updated definitions.

Fields are metric view columns used in`SELECT`,`WHERE`, and`GROUP BY` clauses at query time. Each expression must return a scalar value. Fields can reference columns from the source data or earlier-defined fields in the metric view.

A field can be either:

- A categorical or grouping column, such as a region, status, or department.
- An unaggregated numeric column, such as an age, price, or quantity. Numeric fields can be aggregated at query time using SQL functions such as`SUM` or`AVG`.

Each field definition includes the following properties:

| Property | Type | Description |
| --- | --- | --- |
| `name` | String | Required for explicit column expressions. The column alias for the field. Omit it for wildcard expressions, where Databricks derives names from the source. See Bulk-import fields and measures with wildcards. |
| `expr` | String | Required. A SQL expression that can reference columns from the source data or a previously defined field. Can be a wildcard to import all columns from the source or a joined table. See Bulk-import fields and measures with wildcards. |
| `comment` | String | Optional. Description of the field. Appears in Unity Catalog and documentation tools. |
| `display_name` | String | Optional. Label that appears in visualization tools. Limited to 255 characters. Requires YAML specification 1.1. See Metric view feature availability. |
| `format` | Map | Optional. Format specification for how values are displayed. Requires YAML specification 1.1. See Format specifications. |
| `synonyms` | Array | Optional. Alternative names for AI and BI tools to discover the field. Up to 10 synonyms, each limited to 255 characters. Requires YAML specification 1.1. See Synonyms. |

←✕

| Property | Type | Description |
| --- | --- | --- |
| `name` | String | Required for explicit column expressions. The column alias for the field. Omit it for wildcard expressions, where Databricks derives names from the source. See Bulk-import fields and measures with wildcards. |
| `expr` | String | Required. A SQL expression that can reference columns from the source data or a previously defined field. Can be a wildcard to import all columns from the source or a joined table. See Bulk-import fields and measures with wildcards. |
| `comment` | String | Optional. Description of the field. Appears in Unity Catalog and documentation tools. |
| `display_name` | String | Optional. Label that appears in visualization tools. Limited to 255 characters. Requires YAML specification 1.1. See Metric view feature availability. |
| `format` | Map | Optional. Format specification for how values are displayed. Requires YAML specification 1.1. See Format specifications. |
| `synonyms` | Array | Optional. Alternative names for AI and BI tools to discover the field. Up to 10 synonyms, each limited to 255 characters. Requires YAML specification 1.1. See Synonyms. |

warning

String-like metric view fields are always`STRING`, even when the source column is`CHAR` or`VARCHAR`. Because`CHAR(n)` space-padding is lost, comparisons can return different results. For example,`column = 'COLLEGE'` matches a`CHAR(10)` value in the source table (which is space-padded) but not in the metric view field.

Example:

YAML

```yaml
fields:  # Basic field  - name: order_date    expr: o_orderdate    comment: 'Date the order was placed'    display_name: 'Order Date'  # Field with SQL expression  - name: order_month    expr: DATE_TRUNC('MONTH', o_orderdate)    display_name: 'Order Month'  # Field with synonyms  - name: order_status    expr: CASE      WHEN o_orderstatus = 'O' THEN 'Open'      WHEN o_orderstatus = 'P' THEN 'Processing'      WHEN o_orderstatus = 'F' THEN 'Fulfilled'      END    display_name: 'Order Status'    synonyms: ['status', 'fulfillment status']
```

## Measures​

Measures are expressions that produce results without a pre-determined level of aggregation. They must be expressed using aggregate functions. To reference a measure in a query, use the`MEASURE` function. Measures can reference base columns in the source data, earlier-defined fields, or earlier-defined measures.

Each measure definition includes the following fields:

| Field | Type | Description |
| --- | --- | --- |
| `name` | String | Required for explicit measure expressions. The alias for the measure. Omit it for wildcard expressions, where Databricks derives names from the source. See Bulk-import fields and measures with wildcards. |
| `expr` | String | Required. A SQL expression containing one or more aggregate functions. Can be a wildcard to import all measures from a metric view source. See Bulk-import fields and measures with wildcards. |
| `comment` | String | Optional. Description of the measure. Appears in Unity Catalog and documentation tools. |
| `display_name` | String | Optional. Label that appears in visualization tools. Limited to 255 characters. Requires YAML specification 1.1. See Metric view feature availability. |
| `format` | Map | Optional. Format specification for how values are displayed. Requires YAML specification 1.1. See Format specifications. |
| `synonyms` | Array | Optional. Alternative names for AI and BI tools to discover the measure. Up to 10 synonyms, each limited to 255 characters. Requires YAML specification 1.1. See Metric view feature availability. |
| `window` | Array | Optional. Window specifications for windowed, cumulative, or semiadditive aggregations. When not specified, the measure behaves as a standard aggregate. See Window measures. |

←✕

| Field | Type | Description |
| --- | --- | --- |
| `name` | String | Required for explicit measure expressions. The alias for the measure. Omit it for wildcard expressions, where Databricks derives names from the source. See Bulk-import fields and measures with wildcards. |
| `expr` | String | Required. A SQL expression containing one or more aggregate functions. Can be a wildcard to import all measures from a metric view source. See Bulk-import fields and measures with wildcards. |
| `comment` | String | Optional. Description of the measure. Appears in Unity Catalog and documentation tools. |
| `display_name` | String | Optional. Label that appears in visualization tools. Limited to 255 characters. Requires YAML specification 1.1. See Metric view feature availability. |
| `format` | Map | Optional. Format specification for how values are displayed. Requires YAML specification 1.1. See Format specifications. |
| `synonyms` | Array | Optional. Alternative names for AI and BI tools to discover the measure. Up to 10 synonyms, each limited to 255 characters. Requires YAML specification 1.1. See Metric view feature availability. |
| `window` | Array | Optional. Window specifications for windowed, cumulative, or semiadditive aggregations. When not specified, the measure behaves as a standard aggregate. See Window measures. |

See Aggregate functions for a list of aggregate functions.

Example:

YAML

```yaml
measures:  # Simple count measure  - name: order_count    expr: COUNT(1)    display_name: 'Order Count'  # Sum aggregation measure with synonyms  - name: total_revenue    expr: SUM(o_totalprice)    comment: 'Gross revenue from all orders'    display_name: 'Total Revenue'    synonyms: ['revenue', 'total sales']  # Distinct count measure  - name: unique_customers    expr: COUNT(DISTINCT o_custkey)    display_name: 'Unique Customers'  # Calculated measure combining multiple aggregations  - name: avg_order_value    expr: SUM(o_totalprice) / COUNT(DISTINCT o_orderkey)    display_name: 'Avg Order Value'    synonyms: ['AOV', 'average order']  # Filtered measure with WHERE condition  - name: open_order_revenue    expr: SUM(o_totalprice) FILTER (WHERE o_orderstatus = 'O')    display_name: 'Open Order Revenue'    synonyms: ['backlog', 'outstanding revenue']
```

## Bulk-import fields and measures with wildcards​

Applies to: Databricks Runtime 18.2 and above with YAML specification 1.1

In a`fields` or`measures` definition, you can use a wildcard (`*`) in the`expr` field to import all columns from the source or a joined table without listing each one. This is useful when you want a metric view to expose every column from an upstream asset, similar to`SELECT *` in a standard view. Databricks expands the wildcard to concrete columns when you create or replace the metric view, and derives each column name from the source column name.

Like explicit column definitions, wildcard expressions are expanded when you create the metric view. To pick up columns added to the source later, recreate the metric view with`CREATE OR REPLACE` or`ALTER`.

Wildcards support the following forms:

| Syntax | Description |
| --- | --- |
| `source.*` | Import all columns from the metric view source. |
| `.*` | Import all columns from a joined table, referenced by its join name. Nested joins use the full dot-path, such as`customer.nation.*`. |
| `.* EXCEPT (col1, col2, ...)` | Import all columns from the target except those listed. |
| `..*` | Expand the fields of a`STRUCT` column into separate columns. |

←✕

| Syntax | Description |
| --- | --- |
| `source.*` | Import all columns from the metric view source. |
| `.*` | Import all columns from a joined table, referenced by its join name. Nested joins use the full dot-path, such as`customer.nation.*`. |
| `.* EXCEPT (col1, col2, ...)` | Import all columns from the target except those listed. |
| `..*` | Expand the fields of a`STRUCT` column into separate columns. |

The following rules apply to wildcard expressions:

- Omit the`name` field. Databricks derives column names from the source, so`name` isn't allowed on a wildcard expression.
- Semantic metadata isn't allowed on a wildcard expression. Don't set`comment`,`display_name`,`format`, or`synonyms` on a wildcard. To add metadata to a specific column, exclude it from the wildcard with`EXCEPT` and define it explicitly.
- In a`measures` definition, a wildcard imports measures only from a metric view source. Base tables have no measures, so a wildcard expands to no measures when the source is a base table.
- You can't reference a wildcard-imported column by its derived name in a later`fields` or`measures` expression. Reference the source column with its full path instead.

### Resolve name collisions​

When you import columns from more than one source with a wildcard, columns that share a name (such as`id` or`date`) collide and cause an error when you save the definition. To resolve a collision, exclude the column from each wildcard with`EXCEPT`, then define it explicitly with a unique name:

YAML

```yaml
fields:  - expr: source.* EXCEPT (id)  - expr: customer.* EXCEPT (id)  - name: source_id    expr: source.id  - name: customer_id    expr: customer.id
```

### Wildcard example​

The following definition imports all columns from the source and from a joined table, excludes two columns, and defines one column explicitly to add metadata:

YAML

```yaml
version: 1.1source: samples.tpch.ordersjoins:  - name: customer    source: samples.tpch.customer    on: source.o_custkey = customer.c_custkey    joins:      - name: nation        source: samples.tpch.nation        on: customer.c_nationkey = nation.n_nationkeyfields:  # Import all columns from the source  - expr: source.*  # Import all columns from a joined table, excluding two  - expr: customer.nation.* EXCEPT (n_name, n_comment)  # Define a specific column explicitly to add metadata  - name: nation_name    expr: customer.nation.n_name    comment: "Customer's nation"    display_name: 'Nation Name'
```

## Window measures​

Experimental

This feature is Experimental.

The`window` field defines windowed, cumulative, or semiadditive aggregations for measures. For detailed information about window measures and use cases, see Window measures.

Each window specification includes the following fields:

| Field | Type | Description |
| --- | --- | --- |
| `order` | String | Required. The field that determines the ordering of the window. (1) |
| `range` | String | Required. The extent of the window. See Supported`range` values. |
| `semiadditive` | String | Required. Aggregation method. Supported values:`first` or`last`. |
| `offset` | String | Optional. Requires Databricks Runtime 18.1 and YAML specification version 1.1 or above. Shifts the window frame backward or forward along the`order` field by a fixed interval. The value is of the form` `, where`n` is a signed integer (negative looks backward, positive looks forward) and`period` is one of`day`,`days`,`month`,`months`,`year`, or`years`. Examples:`-12 month`,`1 year`,`-3 days`,`7 day`. The`order` field must be a date or timestamp column.`offset` has no effect on`range: all`. If the shifted frame falls outside the available data, the measure evaluates to`NULL`. For usage and worked examples, see How offset shifts the window frame. |

←✕

| Field | Type | Description |
| --- | --- | --- |
| `order` | String | Required. The field that determines the ordering of the window. (1) |
| `range` | String | Required. The extent of the window. See Supported`range` values. |
| `semiadditive` | String | Required. Aggregation method. Supported values:`first` or`last`. |
| `offset` | String | Optional. Requires Databricks Runtime 18.1 and YAML specification version 1.1 or above. Shifts the window frame backward or forward along the`order` field by a fixed interval. The value is of the form` `, where`n` is a signed integer (negative looks backward, positive looks forward) and`period` is one of`day`,`days`,`month`,`months`,`year`, or`years`. Examples:`-12 month`,`1 year`,`-3 days`,`7 day`. The`order` field must be a date or timestamp column.`offset` has no effect on`range: all`. If the shifted frame falls outside the available data, the measure evaluates to`NULL`. For usage and worked examples, see How offset shifts the window frame. |

(1) The referenced field must be deterministic. Non-deterministic expressions such as`rand()`,`uuid()`, or`current_timestamp()` produce unpredictable window ordering and can lead to incorrect aggregation results.

### Supported range values​

- `current`: Rows where the window ordering value equals the anchor row's value.
- `cumulative`: All rows where the window ordering value is less than or equal to the anchor row's value.
- `trailing [inclusive | exclusive]`: Rows from the anchor row going backward by the specified time units, for example`trailing 7 day`. The optional`inclusive` or`exclusive` modifier requires Databricks Runtime 18.1 and YAML specification version 1.1 or above, and controls whether the anchor row is included in the window. The default is`exclusive`. See Include or exclude the anchor row.
- `leading [inclusive | exclusive]`: Rows from the anchor row going forward by the specified time units, for example`leading 3 month`. The optional`inclusive` or`exclusive` modifier requires Databricks Runtime 18.1 and YAML specification version 1.1 or above, and controls whether the anchor row is included in the window. The default is`exclusive`. See Include or exclude the anchor row.
- `all`: All rows regardless of the window ordering value.

### Window measure example​

The following example calculates a rolling 7-day count of unique customers:

YAML

```yaml
version: 1.1source: samples.tpch.ordersfields:  - name: order_date    expr: o_orderdatemeasures:  - name: rolling_7day_customers    expr: COUNT(DISTINCT o_custkey)    display_name: '7-Day Rolling Customers'    window:      - order: order_date        range: trailing 7 day        semiadditive: last
```

## Materialization​

The`materialization` field configures automatic query acceleration using materialized views. For detailed information about how materialization works, requirements, and best practices, see Materialization for metric views.

note

You can't materialize a metric view that defines parameters.

The`materialization` field includes the following top-level fields:

| Field | Type | Description |
| --- | --- | --- |
| `schedule` | String | Optional. Refresh schedule. Uses the same syntax as the schedule clause on materialized views. If omitted, materializations are refreshed only manually. To trigger a manual refresh, see Manual refresh. The`TRIGGER ON UPDATE` clause is not supported. |
| `mode` | String | Required. Must be set to`relaxed`. |
| `materialized_views` | Array | Required. List of materialized views to materialize. Each entry requires the fields described below. |

←✕

| Field | Type | Description |
| --- | --- | --- |
| `schedule` | String | Optional. Refresh schedule. Uses the same syntax as the schedule clause on materialized views. If omitted, materializations are refreshed only manually. To trigger a manual refresh, see Manual refresh. The`TRIGGER ON UPDATE` clause is not supported. |
| `mode` | String | Required. Must be set to`relaxed`. |
| `materialized_views` | Array | Required. List of materialized views to materialize. Each entry requires the fields described below. |

Each entry in`materialized_views` includes the following fields:

| Field | Type | Description |
| --- | --- | --- |
| `name` | String | Required. The name of the materialization. |
| `type` | String | Required. Type of materialization. Supported values:`aggregated`(requires`dimensions`,`measures`, or both) or`unaggregated`. Only one`unaggregated` entry is allowed per metric view. Unaggregated entries don't use the`dimensions` or`measures` fields. |
| `dimensions` | Array | Conditional. List of field names to materialize, using the`dimensions` keyword even if your top-level definition uses`fields`. Required if`type` is`aggregated` and no`measures` are specified. |
| `measures` | Array | Conditional. List of measure names to materialize. Required if`type` is`aggregated` and no`dimensions` are specified. |
| `cluster_by` | Object | Optional. Clustering columns for the materialization, equivalent to the CLUSTER BY clause on a materialized view. Specify`cols` with a list of column names, or set`auto: true` to let Databricks choose the clustering columns automatically. |
| `partition_by` | Array | Optional. List of columns to partition the materialization by, equivalent to the PARTITION BY clause on a materialized view. |

←✕

| Field | Type | Description |
| --- | --- | --- |
| `name` | String | Required. The name of the materialization. |
| `type` | String | Required. Type of materialization. Supported values:`aggregated`(requires`dimensions`,`measures`, or both) or`unaggregated`. Only one`unaggregated` entry is allowed per metric view. Unaggregated entries don't use the`dimensions` or`measures` fields. |
| `dimensions` | Array | Conditional. List of field names to materialize, using the`dimensions` keyword even if your top-level definition uses`fields`. Required if`type` is`aggregated` and no`measures` are specified. |
| `measures` | Array | Conditional. List of measure names to materialize. Required if`type` is`aggregated` and no`dimensions` are specified. |
| `cluster_by` | Object | Optional. Clustering columns for the materialization, equivalent to the CLUSTER BY clause on a materialized view. Specify`cols` with a list of column names, or set`auto: true` to let Databricks choose the clustering columns automatically. |
| `partition_by` | Array | Optional. List of columns to partition the materialization by, equivalent to the PARTITION BY clause on a materialized view. |

note

The materialization block uses the`dimensions:` keyword rather than`fields:`. Use`dimensions:` when listing fields to materialize, even if your top-level definition uses`fields:`.

### Materialization example​

The following example defines a metric view with multiple materializations:

YAML

```yaml
version: 1.1source: prod.operations.orders_enriched_viewfilter: revenue > 0# filter, fields, and measures can't use invoker-dependent expressions: no current_user(), is_member(), etc.# source can't have RLS, column masking, or ABAC policiesjoins:  - name: customers    source: prod.operations.customers    on: source.customer_id = customers.id    # if one-to-many, all materializations below drop to exact match onlyfields:  - name: category    expr: substring(category, 5)  - name: order_date    expr: order_datemeasures:  - name: total_revenue    expr: SUM(revenue)  - name: number_of_suppliers    expr: COUNT(DISTINCT supplier_id)  - name: revenue_for_open_orders    expr: SUM(revenue) FILTER (WHERE status = 'O')  - name: blended_margin    expr: SUM(revenue) - SUM(cost)  - name: rolling_7day_customers    expr: COUNT(DISTINCT customer_id)    window:      - order: order_date        range: trailing 7 day        semiadditive: lastmaterialization:  schedule: every 6 hours  mode: relaxed  materialized_views:    - name: baseline      type: unaggregated      # only one allowed per metric view; doesn't use dimensions or measures keys      # no benefit if source is an unfiltered direct table reference    - name: daily_status_metrics      type: aggregated      dimensions:        - order_date        - category # avoid overly granular dimensions, such as millisecond timestamps      measures:        - total_revenue # rollup-eligible        - number_of_suppliers # exact match only (non-additive)        - revenue_for_open_orders # rollup-eligible (deterministic filter)        - blended_margin # exact match only (multiple aggregates)        - rolling_7day_customers # exact match only (window measure)      cluster_by:        cols:          - order_date          - category      partition_by:        - order_date
```

## Column name references​

When referencing column names that contain spaces or special characters in YAML expressions, enclose the column name in backticks. If the expression starts with a backtick and is used directly as a YAML value, wrap the entire expression in double quotes. Valid YAML values cannot start with a backtick.

## Formatting examples​

Use the following examples to learn how to format YAML correctly in common scenarios.

### Reference a column name​

The following examples show how to format column references depending on the characters they contain.

#### No spaces​

Source column:`revenue`

YAML

```yaml
expr: "revenue"expr: 'revenue'expr: revenue
```

Use double quotes, single quotes, or no quotes around the column name.

#### Column name with spaces​

Source column:``First Name``

YAML

```yaml
expr: '`First Name`'
```

Use backticks to escape spaces. Enclose the entire expression in double quotes.

#### Column names with spaces in a SQL expression​

Source columns:``First Name``,``Last Name``

YAML

```yaml
expr: CONCAT(`First Name`, ' ', `Last Name`)
```

If the expression doesn't start with a backtick, double quotes are not required.

#### Column name containing quotes​

Source column:`"name"`

YAML

```yaml
expr: '`"name"`'
```

Use backticks to escape the double quotes in the column name. Enclose the expression in single quotes.

### Expressions with colons​

YAML

```yaml
expr: "CASE WHEN `Customer Tier` = 'Enterprise: Premium' THEN 1 ELSE 0 END"
```

note

YAML interprets unquoted colons as key-value separators. Always use double quotes around expressions that include colons.

### Multi-line expressions​

YAML

```yaml
expr: |  CASE WHEN    revenue > 100 THEN 'High'  ELSE 'Low'  END
```

note

Use the`|` block scalar after`expr:` for multiline expressions. All lines must be indented at least two spaces beyond the`expr` key for correct parsing.

## Upgrade to YAML 1.1​

Upgrading a metric view to YAML specification version 1.1 requires care, because comments are handled differently than in earlier versions.

### Types of comments​

- YAML comments (`#`): Inline or single-line comments written directly in the YAML file.
- Unity Catalog comments: Comments stored in Unity Catalog for the metric view or its columns. These are separate from YAML comments.

### Upgrade considerations​

Select the upgrade path that matches how you want to handle comments in your metric view.

#### Option 1: Preserve YAML comments using notebooks or the SQL editor​

If your metric view contains YAML comments (`#`) that you want to keep, use the following steps:

1. Use the`ALTER VIEW` command in a notebook or SQL editor.
2. Copy the original YAML definition into the`$$..$$` section after`AS`. Change the value of`version` to`1.1`.
3. Save the metric view.

SQL

```sql
ALTER VIEW metric_view_name AS$$# The notebook preserves inline commentsversion: 1.1source: samples.tpch.ordersfields:- name: order_date # The notebook preserves inline comments  expr: o_orderdatemeasures:# The notebook preserves commented out definitions# - name: total_orders#   expr: COUNT(o_orderid)- name: total_revenue  expr: SUM(o_totalprice)$$
```

warning

Running`ALTER VIEW` removes Unity Catalog comments unless they are explicitly included in the`comment` fields of the YAML definition. To preserve comments shown in Unity Catalog, see Option 2.

#### Option 2: Preserve Unity Catalog comments​

note

The following guidance applies only when using the`ALTER VIEW` command in a notebook or SQL editor. If you upgrade your metric view to version 1.1 using the YAML editor UI, the YAML editor UI automatically preserves your Unity Catalog comments.

1. Copy all Unity Catalog comments into the appropriate`comment` fields in your YAML definition. Change the value of`version` to`1.1`.
2. Save the metric view.

SQL

```sql
ALTER VIEW metric_view_name AS$$version: 1.1source: samples.tpch.orderscomment: "Metric view of order (Updated comment)"fields:- name: order_date  expr: o_orderdate  comment: "Date of order - Copied from Unity Catalog"measures:- name: total_revenue  expr: SUM(o_totalprice)  comment: "Total revenue"$$
```

For YAML specification version history and minimum runtime requirements for each feature, see Metric view feature availability.

On this page

- Edit YAML in the metric view editor
- Top-level YAML fields
- Source
- - Table-like asset source
- SQL query source
- Parameters
- Filter
- Joins
- - Star schema joins
- Snowflake schema joins
- One-to-many joins
- Optimize joins with`rely`
- Fields
- Measures
- Bulk-import fields and measures with wildcards
- - Resolve name collisions
- Wildcard example
- Window measures
- - Supported`range` values
- Window measure example
- Materialization
- - Materialization example
- Column name references
- Formatting examples
- - Reference a column name
- Expressions with colons
- Multi-line expressions
- Upgrade to YAML 1.1
- - Types of comments
- Upgrade considerations