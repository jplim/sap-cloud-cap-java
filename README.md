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

# Reference

[Build a Business Application Using CAP for Java](https://developers.sap.com/mission.cap-java-app.html)