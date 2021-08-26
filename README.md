# Liquibase bug reproduction: context not being applied

## Setup
```
# Download JARs
curl https://repo1.maven.org/maven2/org/liquibase/liquibase-core/4.4.3/liquibase-core-4.4.3.jar -O -L
curl https://repo1.maven.org/maven2/org/postgresql/postgresql/42.2.23/postgresql-42.2.23.jar -O -L

# Run Postgres in docker
docker run -d -p 7788:5432 --name liquitest --rm -e POSTGRES_PASSWORD=postgres postgres:13.4
```

## Execute migration

```
java -cp liquibase-core-4.4.3.jar:postgresql-42.2.23.jar liquibase.integration.commandline.Main --url "jdbc:postgresql://localhost:7788/postgres" --changeLogFile db.changelog-master.xml --username postgres --password postgres update
```

## Observation

According to the documentation, setting `context` attribute on `<databaseChangelog>` or `includeAll` node should add the specified context value to all changesets within its scope

- Migrations are correctly run but the "default" context is not applied at all
- However, if the changelog itself has some context set (e.g. `some_context`), then the changeset in DATABASECHANGELOG will have a context value of `default AND some_context`, matching the liquibase documentation.

