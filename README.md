Purpose of Nexus IQ Scans in CI Pipeline Execution
Nexus IQ Server is the policy engine of Sonatype that performs Software Composition Analysis (SCA) — scanning your dependencies (Maven, npm, etc.) for:

Open source license issues
Security vulnerabilities (CVEs)
Policy violations

Nexus IQ scans are used in Continuous Integration (CI) pipelines to analyze and assess software dependencies for security vulnerabilities, license compliance, and policy violations. The primary benefits include:

Security Analysis: Detects vulnerabilities in open-source libraries and dependencies.

License Compliance: Ensures adherence to open-source licensing requirements.

Policy Enforcement: Blocks builds if dependencies violate security policies.

Risk Mitigation: Provides remediation guidance for identified vulnerabilities.

Governance & Audit: Helps maintain an audit trail for dependency usage.

The scan itself (dependency collection) happens on the build agent (CloudBees worker).

The policy evaluation happens on the Nexus IQ Server (external to CI).

The scan tool acts as a client, sending data to the IQ Server for final evaluation.

Why is Fortify Application ID Required for Nexus IQ Scans?

The Fortify Application ID is required for Nexus IQ scans because many organizations integrate Fortify (SAST) with Nexus IQ (SCA) for comprehensive security analysis. The Application ID serves the following purposes:

Project Identification: Links the Nexus IQ scan results to a specific FPurpose of Nexus IQ Scans in CI Pipeline Execution
Nexus IQ Server is the policy engine of Sonatype that performs Software Composition Analysis (SCA) — scanning your dependencies (Maven, npm, etc.) for:

Open source license issues
Security vulnerabilities (CVEs)
Policy violations

Nexus IQ scans are used in Continuous Integration (CI) pipelines to analyze and assess software dependencies for security vulnerabilities, license compliance, and policy violations. The primary benefits include:

Security Analysis: Detects vulnerabilities in open-source libraries and dependencies.

License Compliance: Ensures adherence to open-source licensing requirements.

Policy Enforcement: Blocks builds if dependencies violate security policies.

Risk Mitigation: Provides remediation guidance for identified vulnerabilities.

Governance & Audit: Helps maintain an audit trail for dependency usage.

The scan itself (dependency collection) happens on the build agent (CloudBees worker).

The policy evaluation happens on the Nexus IQ Server (external to CI).

The scan tool acts as a client, sending data to the IQ Server for final evaluation.

Why is Fortify Application ID Required for Nexus IQ Scans?

The Fortify Application ID is required for Nexus IQ scans because many organizations integrate Fortify (SAST) with Nexus IQ (SCA) for comprehensive security analysis. The Application ID serves the following purposes:

Project Identification: Links the Nexus IQ scan results to a specific Fortify application profile.

Correlation Between SAST & SCA: Helps correlate static code vulnerabilities (Fortify) with dependency vulnerabilities (Nexus IQ).

Centralized Reporting: Allows consolidated security insights in dashboards.

Policy-Based Actions: Enables automated enforcement of security policies across both SAST and SCA scans.ortify application profile.
