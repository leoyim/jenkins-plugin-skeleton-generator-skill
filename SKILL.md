---
description: Generate Jenkins plugin skeleton using user-provided
  Jenkins version and JDK version.
name: jenkins-plugin-skeleton-generator
---

# Jenkins Plugin Skeleton Generator Skill

## Purpose

Generate a Jenkins plugin foundation matching the user's environment.

This skill MUST NOT assume a default Jenkins version or JDK version.

## Required Inputs Before Execution

Ask the user for:

1.  Jenkins version
2.  JDK version
3.  Plugin type (optional): Builder, Pipeline Step, Action, Management
    Page
4.  plugin name, groupId, artifactId, package name

Do not generate files until Jenkins version and JDK version are
confirmed.

## Workflow

### Environment Collection

Collect: - Jenkins Core version - JDK version - Maven version
(optional) - Plugin type

Analyze: - Jenkins API compatibility - Java language level - Jenkins
Plugin Parent compatibility - Dependency versions

### Generate Project

Create Maven HPI project:

-   pom.xml
-   src/main/java
-   src/main/resources
-   src/main/webapp
-   src/test/java
-   README.md

### Maven Configuration

Generate configuration based on user input:

-   packaging=hpi
-   compatible Jenkins Plugin Parent
-   Jenkins core dependency
-   Maven HPI plugin
-   test dependencies

Avoid: - unsupported APIs - dependency conflicts - servlet namespace
issues

### Plugin Code

Generate Jenkins extension code:

-   @Extension
-   DataBoundConstructor
-   DescriptorImpl
-   Jenkins coding conventions

### Build

Commands:

mvn clean package

mvn hpi:run

Output:

target/\*.hpi

### Installation Validation

Install:

Manage Jenkins -\> Plugins -\> Advanced -\> Upload Plugin

Verify plugin loading and Jenkins logs.

## Compatibility Checklist

Validate against user-provided:

-   Jenkins version
-   JDK version

Check: - Jenkins API compatibility - Java compatibility - Maven
configuration - Dependency convergence

## Output Requirements

Provide: 1. Technical design 2. Directory structure 3. pom.xml 4. Java
source 5. Resource files 6. Build steps 7. Installation steps 8.
Troubleshooting

## Quality Rules

Generated plugin must: - match requested Jenkins version - match
requested JDK version - build successfully - produce HPI package -
follow Jenkins plugin conventions
