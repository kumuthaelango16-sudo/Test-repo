7. Migration Pipelines and Utilities

The Migration Accelerator provides different pipelines to support multiple migration scenarios. These pipelines enable flexible migration of UDeploy entities into CloudBees CD depending on the migration scope.

7.1 Core Migration Pipelines
1. Bulk Migration Pipeline

Suggested Name Options

UDeploy-Bulk-Migration-Pipeline

Bulk-Migration-Accelerator

Enterprise-Bulk-Migration-Pipeline

Purpose
Migrates multiple applications and components in a single execution. This pipeline is typically used during large-scale migration initiatives.

Capabilities

Accepts bulk input list

Migrates applications, components, and processes

Generates consolidated migration report

2. Application Migration Pipeline

Suggested Name Options

Application-Migration-Pipeline

UDeploy-Application-Migration

App-Level-Migration-Pipeline

Purpose
Migrates a specific application from UDeploy to CloudBees CD.

Capabilities

Extract application metadata

Migrate associated components

Migrate application processes

Configure environments

3. Component Migration Pipeline

Suggested Name Options

Component-Migration-Pipeline

UDeploy-Component-Migration

Component-Level-Migration-Pipeline

Purpose
Migrates a single component and its associated configuration.

Capabilities

Export component metadata

Migrate component processes

Convert component steps

Create component in CloudBees CD

7.2 Utility Pipelines

Utility pipelines support migration by handling system-level configurations and artifacts.

1. System Properties Migration Pipeline

Suggested Name Options

System-Properties-Migration

Global-Properties-Migrator

UDeploy-System-Config-Migration

Purpose
Migrates system-level properties from UDeploy to CloudBees CD.

Capabilities

Extract global properties

Map property keys

Create properties in CloudBees CD

2. Version Properties & Artifact Migration Pipeline

Suggested Name Options

Version-Metadata-Migration

Artifact-Version-Migration

Component-Version-Migration

Purpose
Migrates version-level metadata and artifacts associated with components.

Capabilities

Extract component versions

Migrate version properties

Map artifact locations

Re-register artifacts in CloudBees CD

💡 Recommended Naming Convention (Best Practice)

Use a consistent prefix like:

CBCD-UDeploy-Bulk-Migration
CBCD-UDeploy-App-Migration
CBCD-UDeploy-Component-Migration
CBCD-UDeploy-System-Properties-Migration
CBCD-UDeploy-Version-Artifact-Migration

This makes pipelines easy to identify and manage in CloudBees CD.

If you want, I can also help you create a very strong architecture section for the document:

“Accelerator Pipeline Execution Flow” (which architects usually expect in design docs).
