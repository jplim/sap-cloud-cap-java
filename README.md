# sap-cloud-cap-java
SAP Cloud Application Programming Model in Java

# products-service

## Usage

Initialize the application using maven arcetype `cds-services-archtype`.

```
> mvn -B archetype:generate -DarchetypeArtifactId=cds-services-archetype -DarchetypeGroupId=com.sap.cds \
-DarchetypeVersion=RELEASE \
-DgroupId=com.sap.cap -DartifactId=products-service -Dpackage=com.sap.cap.productsservice
```

Alternative

```
> cds init products-service --add java --java:package com.sap.cap.productsservice
```

# Reference

[Build a Business Application Using CAP for Java](https://developers.sap.com/mission.cap-java-app.html)