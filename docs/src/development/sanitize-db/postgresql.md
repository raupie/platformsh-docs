---
title: "Sanitizing databases: PostgreSQL and Django"
sidebarTitle: PostgreSQL and Django
description: Sanitize PostgreSQL data in non-production environments for Django apps.
layout: list
---

{{% sanitize-dbs/intro database="PostgreSQL" framework="Django" %}}

{{% sanitize-dbs/requirements database="PostgreSQL" framework="Django" %}}

{{% sanitize-dbs/sanitize-intro database="PostgreSQL" %}}

{{< codetabs >}}
+++
title=Manually
+++
{{% sanitize-dbs/sanitize-manually database="PostgreSQL" %}}
<--->
+++
title=Using a script with Django and `psql`
+++

Assumptions:

- `users` is the table where all of your PII is stored in the `staging` development database.
- `database` is the relationship name for the PostgreSQL service.

Set up a script by following these steps:

1.  Retrieve service credentials from the `PLATFORM_RELATIONSHIPS` environment variable to use the `psql` command interface.
    Export these values to a [`.environment` file](../variables/set-variables.md#set-variables-via-script)
    or include them directly in the sanitization script.

    ```bash {location=".environment"}
    # Pull credentials from the PLATFORM_RELATIONSHIPS environment variable.
    DB_USER=$(echo $PLATFORM_RELATIONSHIPS | base64 --decode | jq -r '.database[0].username')
    DB_HOST=$(echo $PLATFORM_RELATIONSHIPS | base64 --decode | jq -r '.database[0].host')
    DB_PORT=$(echo $PLATFORM_RELATIONSHIPS | base64 --decode | jq -r '.database[0].port')
    DB_PASS=$(echo $PLATFORM_RELATIONSHIPS | base64 --decode | jq -r '.database[0].password')
    ```

2.  Create an executable sanitizing script by running the following command:

    ```bash
    touch sanitize.sh && chmod +x sanitize.sh
    ```

3.  Make the script sanitize environments with an [environment type](../../administration/users.md#environment-types)
    other than `production`.

    The following example runs only in non-production environments
    and sanitizes the `display_name` and `email` columns of the `users` table.
    Adjust the details to fit your data.

    ```bash {location="sanitize.sh"}
    #!/usr/bin/env bash

    if [ "$PLATFORM_ENVIRONMENT_TYPE" != production ]; then
        # Sanitize data
        PGPASSWORD=$DB_PASS psql -c "UPDATE users SET display_name=substring(md5(display_name||'$PLATFORM_PROJECT_ENTROPY') for 8);" -U $DB_USER -h $DB_HOST -p $DB_PORT
        PGPASSWORD=$DB_PASS psql -c "UPDATE users SET email=substring(md5(email||'$PLATFORM_PROJECT_ENTROPY') for 8);" -U $DB_USER -h $DB_HOST -p $DB_PORT
    fi   
    ```

4.  Update the deploy hook to run your script on each deploy.

    ```yaml {location=".platform.app.yaml"}
    hooks:
        build: ...
        deploy: |
            python manage.py migrate
            bash sanitize.sh
    ```

5.  Commit your changes by running the following command:

    ```bash
    git add .environment sanitize.sh .platform.app.yaml && git commit -m "Add sanitization."
    ```

    Push the changes to `staging` and verify that environment's database was sanitized.
    Once merged to production, all data from future non-production environments are sanitized on environment creation.

{{< /codetabs >}}

## What's next

{{% sanitize-dbs/whats-next %}}

If your database contains a lot of data, consider using the [`REINDEX` statement](https://www.postgresql.org/docs/current/sql-reindex.html) to help improve performance.