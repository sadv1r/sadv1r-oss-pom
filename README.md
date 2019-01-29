# Sadv1r's OSS POM

## Rationale

A base pom allows a new Maven project to quickly get off the ground
and focus on the project itself and not how to build it.
The Sadv1r's OSS POM focuses on building libraries
and components that are distributed as jar files through the Maven
central repository.

It is possible to use the base pom in a new project without any additional changes and modifications.

## Preliminaries

The Sadv1r's OSS POM enforces a minimum version of Maven due to various bugs found in earlier versions.

By default, the Sadv1r's OSS POM enforces JDK 1.8. To use another version, add

```xml
<properties>
  <javaVersion>1.6</javaVersion>
  ...
</properties>
```

### Local setup required!

To fully leverage the deployment options from the Sadv1r's OSS POM, a number of servers need to be configured in the local `~/.m2/settings.xml` file. If these servers are missing, either artifact or site deployment will fail.

As described on https://docs.sonatype.org/display/Repository/Sonatype+OSS+Maven+Repository+Usage+Guide, the `sonatype-nexus-staging` and `sonatype-nexus-snapshots` repositories should be configured:

```xml
<servers>
  ...
  <server>
    <id>ossrh</id>
    <username>user</username>
    <password>password</password>
  </server>
  ...
</servers>
```

To support releasing and tagging repositories that are hosted on Github and to allow site deployment to Github, entries for Github and the project site must exist:

```xml
<servers>
  ...
  <server>
    <id>github</id>
    <username>github-id</username>
    <password>github-password</password>
  </server>
  <server>
    <id>github-project-site</id>
    <username>git</username>
  </server>
  ...
</servers>
```

The hard-coded username `git` and no password for the github-project-site are a limitation of the deployment tool used for the github site. They must exist in the local settings file.

## Usage

Add the Sadv1r's OSS POM as the parent to a project:

```xml
<parent>
  <groupId>ru.sadv1r</groupId>
  <artifactId>sadv1r-oss-pom</artifactId>
  <version> ... current pom release version ...</version>
</parent>
```

## Project pom structure

The following elements should be present in a pom using the Sadv1r's OSS POM as parent:

* `groupId`, `artifactId`, `version`, `packaging`, `name`, `description` and `inceptionYear`

  Define the new project. These elements should always be present. If any of those fields are missing from the project,
the values from the Sadv1r's OSS POM are picked up instead.

* `scm`

  Defines the SCM location and URL for the project. This is required to use the `release:prepare` and `release:perform` targets
  to deploy artifacts to Maven Central.

* `organization`, `developers`, `distributionManagement`

  Empty elements override the values inherited from the Sadv1r's OSS. 

This is a sample skeleton pom using the Sadv1r's OSS POM:

```xml
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">

  <modelVersion>4.0.0</modelVersion>

  <parent>
    <groupId>ru.sadv1r</groupId>
    <artifactId>sadv1r-oss-pom</artifactId>
    <version> ... current version ...</version>
  </parent>

  <groupId> ... group id of the new project ... </groupId>
  <artifactId> ... artifact id of the new project ... </artifactId>
  <version> ... version of the new project ... </version>
  <packaging> ... jar|pom ... </packaging>
  <description> ... description of the new project ...  </description>
  <name>${project.artifactId}</name>
  <inceptionYear>2018</inceptionYear>
  
  <scm>
    <connection> ... scm read only connection ... </connection>
    <developerConnection>... scm read write connection ... </developerConnection>
    <url> ... project url ... </url>
  </scm>

  <distributionManagement/>
  <developers/>
  <organization/>
  ...
</project>
```

## Project POM conventions

In large maven projects, especially with multi-module builds, the pom files can become quite large.
In many places, properties defined in the `<properties>` section of the pom are used. 

To avoid confusion with properties, the following conventions are used in the Sadv1r's OSS POM:

* Properties defined in the POM that influence the build configuration are prefixed with `sa`.
* Properties that factor out plugin versions (because the plugin is used in multiple places in the POM
and the versions should be uniform) start with `dep.plugin` and end with `version`.
* Properties that factor out dependency versions
(to enforce uniform dependency versions across multiple, related dependencies) start with `dep` and end with `version`.

Examples:

```xml
<properties>
  <!-- Sets the minimum maven version to build (influences build) -->
  <sa.maven.version>3.0.4</sa.maven.version>

  <!-- The surefire plugin version -->
  <dep.plugin.surefire.version>2.13</dep.plugin.surefire.version>

  <!-- The version for all guice related dependencies -->
  <dep.guice.version>3.0</dep.guice.version>
</properties>
```

## Deploy to Maven Central (oss.sonatype.org)

The Sadv1r's OSS POM is intended for open source projects that should be deployed to Maven Central. 

It supports the OSS deployment process as outlined at https://docs.sonatype.org/display/Repository/Sonatype+OSS+Maven+Repository+Usage+Guide.

