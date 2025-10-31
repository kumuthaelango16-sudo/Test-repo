def verifyBinaryAndCleanup(String basePath, String versionFolder, String fileName) {

    def filePath = "${basePath}/${versionFolder}/${fileName}"
    steps.echo "Checking file: ${filePath}"

    // Check if file exists
    if (steps.fileExists(filePath)) {
        steps.echo "✅ Binary found: ${filePath}"
        return true
    }

    steps.echo "❌ ERROR: Binary not found at ${filePath}"

    // Check and cleanup version directory if empty
    def versionPath = "${basePath}/${versionFolder}"

    try {
        def fileCount = steps.sh(
            script: "ls -1 ${versionPath} 2>/dev/null | wc -l",
            returnStdout: true
        ).trim()

        if (fileCount == "0") {
            steps.echo "🧹 Version folder empty. Cleaning: ${versionPath}"
            steps.sh "rm -rf ${versionPath}"
        } else {
            steps.echo "Folder has files. Skipping cleanup for ${versionPath}"
        }
    } catch (Exception ex) {
        steps.echo "⚠️ Unable to list folder: ${versionPath} — Maybe it doesn't exist."
    }

    steps.error("Stopping pipeline. Required binary missing: ${fileName}")
    return false
}
