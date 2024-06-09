# Base Apache Airflow

Repo: https://github.com/apache/airflow

- Specific Operators: https://github.com/apache/airflow/tree/main/airflow/operators
- BaseOperator: https://github.com/apache/airflow/blob/main/airflow/models/baseoperator.py
- BaseNotifier: https://github.com/apache/airflow/tree/main/airflow/notifications
- BaseHook: https://github.com/apache/airflow/blob/main/airflow/hooks/base.py

# Provider Packages

Provider packages in: https://github.com/apache/airflow/tree/main/airflow/providers
- Whilst provider packages are in the same repo as the base apache-airflow, the provider package versions are independent, and typically don't have narrow requirements on the version of Apache Airflow used
- The provider packages can be installed independently with specific versions, or as extras with apache airflow. The latter makes versioning harder:
  -  `apache-airflow-providers-providerX==desired-providerX-package-version`
  -  `apache-airflow[providerX]==desired-airflow-version` (does not control for provider package version which can change over time)

- The typical provider code is for the main logic to be performed in a provider's hook, then the `ProviderOperator.execute()` and `ProviderNotifier` (`.notify()`) methods call the hook.
- The hooks call the BaseHook's `get_connection()` method to get a connection from the connection ID.
  - This calls Connection.get_connection_from_secrets(conn_id) 
    - In Cloud Composer Connections can be automatically fetched from Secrets Manager, provided the secret aligns with prefix setting:
      - eg. secret: `airflow-secrets-slack-conn-id` aligning to prefix `airflow-secrets-` and conn_id `slack-conn-id`
      - A provider's hook definition will indicate what fields are expected in a connection/secret (which can be deployed using a JSON file) 
```
        conn = self.get_connection(conn_name)
        conn = snowflake.connector.connect(
            
            # expected as key-value in connection/secret
            account=conn.host,
            user=conn.login,
            password=conn.password,
            schema=conn.schema, 

            # expected as key-values as the value for the key "extras" in connection/secret
            database=conn.extra_dejson.get('database'), 
            warehouse=conn.extra_dejson.get('warehouse'),
            role=conn.extra_dejson.get('role'),
            region=conn.extra_dejson.get('region'),
            autocommit=conn.extra_dejson.get('autocommit'),
        )
```