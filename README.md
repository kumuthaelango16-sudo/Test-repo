Title:
Automate SSL Import Monitoring and Renewal Pipeline

Description:
Implement an automated monitoring pipeline to track SSL certificate expiry. If a certificate is set to expire within 2 days, the pipeline should automatically import the renewed SSL certificate and place it in the designated target folders. This automation aims to reduce manual intervention and prevent service disruptions due to expired certificates.

Acceptance Criteria:

The pipeline should monitor SSL certificate expiry dates daily.

If an SSL certificate is expiring within 2 days, it should automatically trigger the import process.

The renewed SSL certificate must be added to the correct target folders.

A notification or log entry should confirm successful import and deployment.

The process should handle both on-prem and cloud environments (if applicable).
