DBT is aimed for "analytical engineering"... not for Loading, just for Transforming.

Main package is  DBT-core: https://github.com/dbt-labs/dbt-core

Other packages to get dbt working with 3rd party software exist in the same GitHub organisation (`dbt-labs`), but in different repos, eg.:
- https://github.com/dbt-labs/dbt-snowflake
- https://github.com/dbt-labs/dbt-bigquery

Basic commands: 
- cheatsheet - https://popsql.com/learn-dbt/dbt-commands
- implementation - https://github.com/dbt-labs/dbt-core/blob/main/core/dbt/cli/main.py

Project components (dbt enforces the top-level structure of a dbt project):
- `dbt_project.yml`(config key-values, see https://docs.getdbt.com/docs/build/projects#project-subdirectories)
- `models` - SQL select statements (notably with jinja functions), wrapped in CREATE TABLE/VIEW statements when `dbt run` is run
- `sources` - yaml files that define data sources, so they can be referenced with `{{ source() }}` 
- `analysis`- Any .sql files found in the analyses/ directory of a dbt project will be compiled, but not executed.
- `seeds` - CSV files in your dbt project (typically in your seeds directory), that dbt can load into your data warehouse using the dbt seed command.
- `snapshots` - Configure dbt to maintain tables that track row changes in tables.
- `exposures` - Run, test, and list resources that feed into downstream consumers

DBT Jinja functions: https://datacoves.com/post/dbt-jinja-functions-cheat-sheet

Project Structure:
See https://docs.getdbt.com/best-practices/how-we-structure/1-guide-overview

```
jaffle_shop
├── README.md
├── analyses
├── seeds
│   └── employees.csv
├── dbt_project.yml
├── macros
│   └── cents_to_dollars.sql
├── models
│   ├── intermediate
│   │   └── finance
│   │       ├── _int_finance__models.yml
│   │       └── int_payments_pivoted_to_orders.sql
│   ├── marts
│   │   ├── finance
│   │   │   ├── _finance__models.yml
│   │   │   ├── orders.sql
│   │   │   └── payments.sql
│   │   └── marketing
│   │       ├── _marketing__models.yml
│   │       └── customers.sql
│   ├── staging
│   │   ├── jaffle_shop
│   │   │   ├── _jaffle_shop__docs.md
│   │   │   ├── _jaffle_shop__models.yml
│   │   │   ├── _jaffle_shop__sources.yml
│   │   │   ├── base
│   │   │   │   ├── base_jaffle_shop__customers.sql
│   │   │   │   └── base_jaffle_shop__deleted_customers.sql
│   │   │   ├── stg_jaffle_shop__customers.sql
│   │   │   └── stg_jaffle_shop__orders.sql
│   │   └── stripe
│   │       ├── _stripe__models.yml
│   │       ├── _stripe__sources.yml
│   │       └── stg_stripe__payments.sql
│   └── utilities
│       └── all_dates.sql
├── packages.yml
├── snapshots
└── tests
    └── assert_positive_value_for_total_amount.sql
```

Cookicutter: https://github.com/datacoves/cookiecutter-dbt/tree/main
(re: org, The Datacoves platform helps enterprises solve data analytics challenges with managed dbt, Airflow, and VS Code.)

