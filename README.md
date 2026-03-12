UDeploy to CBCD Migration Accelerator Pipeline
Enterprise Architecture & Detailed Design Document
Version: 1.0
Date: March 2026
1. Executive Summary
This document presents the enterprise architecture and detailed design for the UDeploy to CloudBees CD/RO (CBCD) Migration Accelerator Pipeline.
The purpose of this accelerator is to automate migration of deployment configurations from IBM UrbanCode Deploy to CloudBees CD/RO.
The pipeline enables DevOps teams to migrate applications rapidly, safely, and consistently by extracting configuration artifacts from the source
platform and recreating them in the target platform using standardized APIs.

The migration accelerator minimizes manual configuration effort, reduces human error, and provides a repeatable framework for migrating
multiple applications across environments.
2. Business Context
Many enterprises currently using UrbanCode Deploy are transitioning to modern DevOps platforms such as CloudBees CD/RO.
Manual migration of deployment configurations is time consuming and error prone.

Key business drivers:
• Platform modernization
• Reduction of operational overhead
• Standardized CI/CD processes
• Improved scalability and maintainability

The migration accelerator provides a scalable approach for migrating hundreds of applications across multiple environments.
3. Goals and Objectives
Primary Goals
• Automate migration of deployment configurations
• Reduce manual configuration effort
• Enable consistent migration across applications

Secondary Goals
• Provide reusable pipeline architecture
• Support large scale enterprise migrations
• Provide validation and audit capabilities
4. Scope
In Scope
• Application migration
• Component migration
• Component properties migration
• Environment migration
• Environment properties migration
• Application processes
• Component processes

Out of Scope
• Historical deployment data
• Deployment inventory
• Plugin migration
• Security role migration
5. Assumptions
• Source UrbanCode Deploy APIs are accessible
• Target CloudBees CD/RO APIs are accessible
• Authentication credentials are available
• Application naming conventions are consistent
• Migration is performed during controlled migration windows
6. Constraints
• API rate limits
• Network connectivity between systems
• Platform version compatibility
• Security restrictions
7. High Level Architecture
Architecture Overview

User triggers migration pipeline
        |
        v
Migration Accelerator Pipeline
        |
        v
UDeploy API Layer (Source)
        |
        v
Data Extraction Module
        |
        v
Transformation Engine
        |
        v
CBCD API Layer (Target)
        |
        v
Object Creation
        |
        v
Validation & Reporting
8. Architecture Components
User Interface
Pipeline is triggered manually or via CI/CD orchestration tools.

Migration Pipeline
Responsible for orchestrating migration tasks.

Extraction Engine
Collects configuration artifacts using UDeploy APIs.

Transformation Engine
Converts source configuration into CBCD compatible format.

Creation Engine
Creates objects in CBCD via APIs.

Validation Module
Verifies migration completeness and accuracy.
9. Pipeline Input Parameters
Application Name – Target application for migration
Source UDeploy URL – Source system endpoint
Target CBCD URL – Target system endpoint
Credentials – Authentication credentials
Migration Mode – Full or Partial
10. Functional Workflow
Step 1: Pipeline Trigger
User triggers migration pipeline and provides application name.

Step 2: Application Discovery
Pipeline retrieves application metadata.

Step 3: Component Extraction
Associated components are extracted.

Step 4: Property Extraction
Component and environment properties are retrieved.

Step 5: Environment Extraction
Environment definitions are collected.

Step 6: Process Extraction
Application and component processes are extracted.

Step 7: Data Transformation
Extracted configuration is transformed.

Step 8: CBCD Object Creation
Equivalent objects are created in CBCD.

Step 9: Validation
Migration output is validated.
11. Data Extraction Design
Extraction uses REST APIs from UrbanCode Deploy.

Entities extracted:
• Application metadata
• Components
• Component properties
• Environments
• Environment properties
• Application processes
• Component processes

Extraction output is stored in structured JSON files for transformation.
12. Data Transformation Design
Transformation layer converts source data into CBCD compatible schema.

Transformation includes:
• Object mapping
• Property normalization
• Dependency resolution
• JSON structure conversion
13. Object Mapping
UrbanCode Deploy Object -> CloudBees CD/RO Object

Application -> Project
Component -> Application Service
Component Property -> Property
Environment -> Environment
Component Process -> Procedure
Application Process -> Pipeline
14. CBCD Object Creation
Objects are created sequentially to preserve dependencies.

Creation Order
1. Project
2. Application Services
3. Environments
4. Procedures
5. Pipelines
15. API Integration
Source APIs
• application/info
• component/listInApplication
• component/getProperties
• environment/list
• environment/getProperties
• applicationProcess/list
• componentProcess/list

Target APIs
• createProject
• createApplication
• createEnvironment
• createProcedure
• createPipeline
16. Error Handling
Common Errors
• Application not found
• API failures
• Property conflicts
• Authentication errors

Mitigation
• Retry mechanisms
• Logging and alerts
• Validation checks
17. Logging and Monitoring
Logs are generated for each pipeline stage.

Log format
[TIMESTAMP] [MODULE] [STATUS] MESSAGE

Example
2026-03-12 10:22:01 EXTRACTION SUCCESS Components fetched
18. Security Design
Security considerations include:

• Token based authentication
• Secure credential storage
• Encryption of sensitive properties
• Access control validation
19. Performance Design
Performance optimizations:

• Parallel component processing
• Batched API requests
• Token reuse
• Efficient data transformation
20. Validation Strategy
Validation checks include:

• Application creation validation
• Component count comparison
• Environment validation
• Pipeline verification
21. Rollback Strategy
Rollback mechanisms include:

• Deleting created objects in CBCD
• Re-running migration after correction
• Partial migration recovery
22. Deployment Strategy
Migration pipeline is deployed in enterprise CI/CD infrastructure.
Pipeline execution can be triggered manually or scheduled during migration windows.
23. Operational Support
Operational teams monitor migration runs and review logs.
Alerts are generated for failed migrations.
24. Future Enhancements
Future capabilities:

• Plugin migration
• Deployment inventory migration
• Role migration
• Migration reporting dashboards
• Self-service migration portal
25. Conclusion
The UDeploy to CBCD Migration Accelerator provides a robust and scalable approach to migrating deployment configurations
from legacy DevOps platforms to modern continuous delivery systems.

The accelerator reduces migration complexity, ensures configuration consistency, and supports enterprise scale migrations.
