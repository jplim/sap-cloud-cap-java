# sap-cloud-cap-java
SAP Cloud Application Programming Model in Java

# products-service

## Usage

### Initialize the project

Initialize the application using maven arcetype `cds-services-archtype`.

```bash
> mvn -B archetype:generate -DarchetypeArtifactId=cds-services-archetype -DarchetypeGroupId=com.sap.cds \
-DarchetypeVersion=RELEASE \
-DgroupId=com.sap.cap -DartifactId=products-service -Dpackage=com.sap.cap.productsservice
```

Alternative.

```bash
> cds init products-service --add java --java:package com.sap.cap.productsservice
```

### Setup SQLite

Install SQLite.

```bash
> npm install --save-dev sqlite3
```

Initialize the database with the defined domain model.

```bash
> cds deploy --to sqlite
```

Configure CAP application to use SQLite database.

1. Go to `srv/src/main/resources`, locate and open the `application.yaml` and modify the file as follow:
2. For the field `url`, replace the string `"jdbc:sqlite::memory:"` with a reference to your local database file `"jdbc:sqlite:/home/user/projects/products-service/sqlite.db"`
3. Set the value of `initialization-mode` from `always` to `never`, because you've already initialized the database when running `cds deploy --to sqlite`.

```yaml
---
spring:
  profiles: default
  datasource:
    url: "jdbc:sqlite:/home/user/projects/products-service/sqlite.db"
    driver-class-name: org.sqlite.JDBC
    initialization-mode: never
    hikari:
      maximum-pool-size: 1
```

# bookstore

## Usage

### Initialize the project

Initialize the application using maven arcetype `cds-services-archtype`.

```bash
> mvn -B archetype:generate -DarchetypeArtifactId=cds-services-archetype -DarchetypeGroupId=com.sap.cds \
-DarchetypeVersion=RELEASE \
-DgroupId=com.sap.cap -DartifactId=bookstore
```

### Reuse service as npm dependency for products-service

Install the reusable service project as npm dependency

```bash
> npm install $(npm pack ../products-service -s)
```

Install all other packages and simplify the overall dependency structure `npm dedupe`.

```bash
> npm install && npm dedupe
```

# VSCode Configs

## Launch config

Locate in `".vscode/launch.json"`.

```json
{
    "configurations": [
      {
        "type": "java",
        "name": "CodeLens (Launch) - Application",
        "request": "launch",
        "mainClass": "com.sap.cap.bookstore.Application",
        "projectName": "bookstore"
      },
      {
        "name": "Run bookstore",
        "type": "java",
        "request": "launch",
        "mainClass": "com.sap.cap.bookstore.Application",
        "projectName": "bookstore",
        "preLaunchTask": "Build bookstore",
        "cwd": "${workspaceFolder}",
        "args": [
          "--spring.profiles.active=default"
        ],
        "env": {
          "run.config": "{\"handlerId\":\"cap_run_config_handler_id\",\"runnableId\":\"{\\\"projectPath\\\":\\\"/path_to_bookstore\\\",\\\"profileName\\\":\\\"default\\\"}\"}"
        }
      }
    ],
    "version": "0.2.0"
  }
```

## Tasks config

Locate in `".vscode/tasks.json"`.

```json
{
  "version": "2.0.0",
  "tasks": [
    {
      "label": "Build products-service",
      "type": "shell",
      "command": "mvn clean install",
      "options": {
        "cwd": "${workspaceFolder}"
      }
    }
  ]
}
```

# Reference

[Build a Business Application Using CAP for Java](https://developers.sap.com/mission.cap-java-app.html)