Stage 4: Security Scan (Fortify Upgrade & Validation)
Injection attacks: SQL injection, command injection, where malicious code is injected into user input to execute unintended database queries or system commands. 

Cross-site scripting (XSS): Injecting malicious JavaScript code into a web page to steal user data or perform unauthorized actions. 

Broken authentication: Weak password policies, lack of proper session management, allowing unauthorized access to sensitive features. 

Just like SonarQube, Fortify integrates with CloudBees CI pipelines to perform static application security testing (SAST) — but with some key differences in how and where it operates.

🔧 How Fortify Works in CloudBees Pipeline
Pipeline execution starts on a CloudBees build agent (worker node).
Fortify tools like sourceanalyzer (SCA) or FortifyScanCentral are installed or pulled into the agent.
Fortify Client -CLI utility to interact with Fortify SSC (Software Security Center).

Fortify performs the following steps:

🧩 Typical Fortify Workflow in Pipeline
Step 1: Translate (Static Code Capture)

Captures code and builds dependencies.
sourceanalyzer -b myapp mvn clean compile 
This step collects code, dependencies, and dataflow information into a local Fortify project (in the build agent).

Step 2: Scan
Analyzes the collected data for security issues.
sourceanalyzer -b myapp -scan -f myapp.fpr 
Generates an .fpr(fortify project report) file with vulnerabilities.

Step 3 (Optional): Upload to SSC (Fortify Software Security Center)
Fortify Client -CLI utility to interact with Fortify SSC (Software Security Center).
fortifyclient uploadFPR -file myapp.fpr -project MyApp -version 1.0 
This uploads results to Fortify SSC for web-based viewing, dashboards, policy checks, etc.

Fortify SSC: A web-based Fortify portal for
Viewing scan results

📍 Where the Scan Happens

Translate and scan happens on the CloudBees build agent (local, dynamic, or container-based).

Analysis and dashboards happen in Fortify SSC, if configured.

"Once the Fortify security scan validation is complete, the pipeline moves to the next stage.


------------------------------

Key Fortify Components Explained

📁 1. .fpr (Fortify Project Results)

File type: Fortify Project Results file

Purpose: Stores the results of the static code analysis — all vulnerabilities found, issue metadata, and metrics.

Usage:

Can be opened in Fortify Audit Workbench (local GUI tool for manual review).

Can be uploaded to Fortify SSC for centralized dashboards, reporting, and policy checks.

🧠 2. sourceanalyzer

Tool name: The main CLI tool of Fortify SCA (Static Code Analyzer)
Used for:

Translate step: Captures the code and dependencies during build.
Scan step: Analyzes the captured data for security flaws.
Example:
sh
Copy code
sourceanalyzer -b myapp mvn clean compile # Translation sourceanalyzer -b myapp -scan -f myapp.fpr # Scan and generate .fpr 
-b is a build ID to link translation and scan steps.

🔧 3. fortifyclient (aka Fortify CLI or Fortify SSC Client)
Purpose: CLI utility to interact with Fortify SSC (Software Security Center).

Used for:

Uploading .fpr files to SSC

Managing projects and versions

Example:

sh

Copy code

fortifyclient uploadFPR -file myapp.fpr -project MyApp -version 1.0 

🖥️ 4. SSC (Software Security Center)

What it is: A web-based Fortify portal for:

Viewing scan results

Managing projects and applications

Defining and enforcing security policies

Integrating with CI/CD (quality gates)

Features:

Dashboards and issue tracking
Audit workflows
Integration with ticketing (e.g., JIRA)

