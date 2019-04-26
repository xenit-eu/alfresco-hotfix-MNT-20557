# Hotfix for Alfresco issue [MNT-20557](https://issues.alfresco.com/jira/browse/MNT-20557)

The Alfresco Module Package build by this project will install two **empty** jars in the `WEB-INF/lib` folder
of the Alfresco distribution: `jsr250-api-1.0.jar` and `geronimo-annotation_1.0_spec-1.1.1.jar`. This 
will result in an Alfresco that has only one implementation of the JSR-250 'Common Annotations' specification 
on the classpath.