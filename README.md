Story Title:

POC: Pass Sonar profile as a parameter in CloudBees pipeline to run source code analysis

Story Description:

As part of this POC, we need to enable the CloudBees CI pipeline to dynamically accept a SonarQube profile as an input parameter. The selected profile from the dropdown should be passed to the pipeline, which will then trigger a SonarQube scan of the source code using the specified profile.

This approach will help us validate whether multiple Sonar profiles can be used within the same pipeline based on user selection and improve flexibility in code quality analysis.

The implementation involves:

Creating a parameterized build option in the CloudBees pipeline to select a Sonar profile from a dropdown list.

Passing the selected profile to the SonarQube scan step in the Jenkinsfile (or shared library).

Validating that the pipeline executes successfully and the analysis report is generated in SonarQube using the chosen profile.

Acceptance Criteria:

✅ AC1: Pipeline should display a dropdown parameter to select the Sonar profile (e.g., Profile_A, Profile_B, Profile_C).

✅ AC2: The selected Sonar profile should be passed as a parameter to the SonarQube analysis stage in the pipeline.

✅ AC3: The SonarQube scan should execute using the corresponding profile configuration and rules.

✅ AC4: SonarQube dashboard should reflect that the analysis ran under the selected profile (verify via project settings or analysis logs).

✅ AC5: Pipeline execution logs should clearly display which profile was used during the scan.

✅ AC6: Documentation should be created outlining how to configure and use the Sonar profile parameter in the pipeline.

Additional Notes:

This is a POC to validate dynamic profile selection; production implementation will follow after successful validation.

SonarQube server and credentials must be pre-configured in CloudBees.

Can leverage parameters block in Jenkinsfile and -Dsonar.profile property in Sonar scanner execution.
