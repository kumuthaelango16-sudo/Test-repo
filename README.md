SAST VS DAST
SAST (Static Application Security Testing) - white box
Black-box testing (analyzes running application)
SAST - before deployment catches issues early
DAST - test the api and web interfaces

How Sonar Scan Works in CloudBees Pipeline
SonarRunner (now known as SonarScanner) is a command-line tool used to analyze source code and send the results to SonarQube  for code quality analysis
Pipeline starts (typically from Jenkinsfile in a repo).
Build environment is prepared (agent is provisioned – either static or dynamic worker).
Sonar scanner is invoked within a stage of the pipeline:
For Maven: mvn sonar:sonar
For Gradle: ./gradlew sonarqube
For Node.js: sonar-scanner or via npm script
Scan happens locally on the build agent, collecting:
Source code
Test results
Code coverage (if configured)
Static analysis
The scanner pushes results to the SonarQube server:
Typically via sonar.host.url
Authentication via token

SonarQube server then analyzes the data, stores it, and displays it on the dashboard.

📍 Where the Scan Happens

The actual scan (code parsing and metrics collection) happens on the CloudBees build agent (worker node).
The analysis and reporting (rules, quality gate, etc.) happen on the SonarQube server.
Think of it as: "Scan runs locally in the pipeline container/agent, then analysis is finalized remotely in SonarQube."
----_----_----------------------_-----------------------------------
✅ 

Let me know your tech stack (Node, Maven, etc.), and I can show a tailored example.

SonarRunner (now known as SonarScanner) is a command-line tool used to analyze source code and send the results to SonarQube  for code quality analysis. It acts as the client-side scanner in the SonarQube ecosystem, processing source code and submitting metrics, issues, and other insights.

How SonarRunner (SonarScanner) Works During Sonar Scans
Prepares the Analysis
Reads the configuration from sonar-project.properties, environment variables, or command-line arguments.
Identifies the programming languages, project structure, and rulesets to apply.
Processes the Code
Parses the source code files.
Computes static code analysis metrics like code coverage, bugs, vulnerabilities, code smells, duplications, and maintainability.
Extracts metadata such as lines of code (LOC), complexity, and dependencies.

Interacts With SonarQube Server
Sends the analyzed data to the SonarQube server for further processing.
Retrieves quality profiles, rules, and settings.
Final Reporting
The results are stored in SonarQube, where developers can view them through the web UI.
If any quality gates fail, it can break the CI/CD pipeline or notify developers.
Types of Sonar Scanners

SonarScanner CLI – Standalone command-line tool.
SonarScanner for Maven – Integrated with Maven builds (mvn sonar:sonar).
SonarScanner for Gradle – Works with Gradle (sonarqube plugin).
SonarScanner for MSBuild – For .NET projects.
SonarScanner inside Jenkins, GitHub Actions, or CI/CD Pipelines – Automated execution during builds.
