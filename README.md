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

Alternative.

```bash
> cds init bookstore --add java --java:package com.sap.cap.bookstore
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
    url: "jdbc:sqlite:/home/user/projects/bookstore/sqlite.db"
    driver-class-name: org.sqlite.JDBC
    initialization-mode: never
    hikari:
      maximum-pool-size: 1
```

### Deploy database artifacts to SAP HANA

1. Login to Cloud Foundry with CF CLI.
   
2. Install hdi-deploy node package:
   ```bash
   > npm install --save-dev @sap/hdi-deploy
   ```

3. Add following configuration to file `.cdrsc.json` in root folder of `bookstore` project.
   ```json
   { "hana" : { "deploy-format": "hdbtable" } }
   ```
   `.hdbtable` and `.hdbview` files will be generated in the `(gen/)db/src/gen/` folder.

4. Create an SAP HANA service instance and implicitly push all artifacts to the database using:
   ```bash
   > cds deploy --to hana:bookstore-hana
   ```

5. Configure application to use SAP HANA locally. Edit `pom.xml` in the `srv` directory and add the `<dependencies>` tag.
   ```xml
   <dependency>
       <groupId>com.sap.cds</groupId>
       <artifactId>cds-feature-hana</artifactId>
   </dependency>
   ```

6. Restart the application with SAP HANA connectivity.
   ```bash
   > mvn spring-boot:run -Dspring-boot.run.profiles=cloud
   ```

### Deploy CAP Java App to SCP

1. Create a CF app manifest called `manifest.yml` file in `bookstore` project folder.
   ```yaml
   ---
   applications:
   - name: bookstore
     path: srv/target/bookstore-exec.jar
     random-route: true
     services:
     - bookstore-hana
   ```

2. Enable auto-configuration of SAP HANA db connection.
   Add the following dependency under `<dependencies>` tag and save it to `pom.xml` file.
   ```xml
   <dependency>
       <groupId>com.sap.cds</groupId>
       <artifactId>cds-feature-cloudfoundry</artifactId>
   </dependency>
   ```

### Push the application

1. Build the application.
   ```bash
   > mvn clean install
   ```

2. Push the application to the cloud.
   ```bash
   > cf push
   ```

3. To retrieve the application URL.
   ```bash
   > cf app bookstore
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