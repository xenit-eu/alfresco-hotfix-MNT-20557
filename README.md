# Hotfix for Alfresco issue [MNT-20557](https://issues.alfresco.com/jira/browse/MNT-20557)

## Description

[MNT-20557](https://issues.alfresco.com/jira/browse/MNT-20557) issue description (credits to Axel Faust):

> Alfresco Content Services 6.x includes `javax.annotation-api-1.2.jar`, `jsr250-api-1.0.jar` and `geronimo-annotation_1.0_spec-1.1.1.jar`. All three JARs are essentially providing the same spec API, albeit from different source projects and in different versions. They overlap in 11 classes which are common in the 1.0 spec version of javax.annotation, with javax.annotation-api-1.2.jar providing a more extensive set of classes.
>
> `jsr250-api-1.0.jar` and `geronimo-annotation_1.0_spec-1.1.1.jar` are direct dependencies in alfresco-repository, while `javax.annotation-api-1.2.jar` is a transitive dependency in alfresco-data-model via TIKA parsers.

There are multiple instances of duplicate class definitions on the classpath, but this one in particular blows up with Java 11 in Alfresco 6.1 when you try to use Spring annotations. 

## Workaround

The Alfresco Module Package provided by this project will **overwrite** the two conflicting JARs with two **empty** jars in the `WEB-INF/lib` folder of the Alfresco distribution: `jsr250-api-1.0.jar` and `geronimo-annotation_1.0_spec-1.1.1.jar`. This effectivly removes the conflicting classes from the classpath and only one correct implementation of the JSR-250 'Common Annotations' specification remains.

## Artifact Coordinates

The AMP artifact is available in Maven Central:

```xml
<dependency>
  <groupId>eu.xenit.alfresco</groupId>
  <artifactId>alfresco-hotfix-MNT-20557</artifactId>
  <version>1.0.1</version>
  <type>amp</type>
</dependency>
```


## Problem analysis

> The `javax.annotation.Resource` class in `jsr250-api-1.0.jar` does not define / provide the `String lookup()` method, while the `javax.annotation-api-1.2.jar` (Alfresco 6.0) and `javax.annotation-api-1.3.2.jar` (Alfresco 6.1) does, as does the Oracle Java 8 core library `rt.jar`.

This means this was never a problem on JDK8, because the class was loaded from the bootstrap-classloader and never from the system classpath.
In Java 9 some Java EE modules were deprecated and finally removed in Java 11. This means that `javax.annotation.Resource` is no longer available from the bootstrap classpath, but should be loaded from `javax.annotation:javax.annotation-api` instead.
