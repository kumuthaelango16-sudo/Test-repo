Implementation Plan: Replacing EC-JIRA with EC-REST in CloudBees CD
Objective

The goal of this change is to replace the EC-JIRA plugin, which will no longer be supported after September 30, 2025, with the EC-REST plugin in CloudBees CD. This ensures that all self-service deployments continue to work smoothly without disruption.

Scope

This change covers:

Updating all Create Release processes to use EC-REST instead of EC-JIRA.

Updating all Property Merge configurations.

Updating all Deployment Templates.

Testing and validating that JIRA integrations continue to work after the update.

Not in Scope:

Any non-CloudBees CD JIRA integrations.

New feature requests or enhancements outside this plugin change.

Impact Analysis (After Implementation)

All deployments in CloudBees CD will now use EC-REST.

JIRA ticket operations (create, update, property mapping) will keep working as expected.

Teams may need to adjust slightly to new configuration settings in EC-REST.

Business operations and release processes will continue without interruption.

Risk is minimal after testing, but if issues arise, they can be handled by rollback or fixes.

Backout Plan

During implementation, a backup will be taken for all templates, property merges, and release processes.

If any issue occurs during deployment (before September 30):

All changes will be reverted to the previous state using the backup.

Any minor issues will be fixed with manual configuration adjustments.

If issues occur after September 30:

Rollback to EC-JIRA will not be possible as it will no longer be supported.

Issues will be debugged and fixed directly in EC-REST.

Do you also want me to add a “Validation Steps” section (like checklist items to confirm everything works after change)? That will make your document look complete and audit-ready.
