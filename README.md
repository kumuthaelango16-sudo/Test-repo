def verifyBinaryAndCleanup(String basePath, String versionFolder, String fileName) {

    def filePath = "${basePath}/${versionFolder}/${fileName}"
    def versionPath = "${basePath}/${versionFolder}"

    steps.echo "Checking file: ${filePath}"

    // ✅ Check if version folder exists first
    if (!steps.fileExists(versionPath)) {
        steps.echo "❌ ERROR: Version folder not found: ${versionPath}"
        steps.error("Stopping pipeline. Version directory missing: ${versionFolder}")
        return false
    }

    // ✅ Check if binary file exists
    if (steps.fileExists(filePath)) {
        steps.echo "✅ Binary found: ${filePath}"
        return true
    }

    steps.echo "❌ ERROR: Binary not found at ${filePath}"

    // ✅ Folder exists but file not present — check folder content
    def fileCount = steps.sh(
        script: "shopt -s nullglob dotglob; files=(${versionPath}/*); echo \${#files[@]}",
        returnStdout: true
    ).trim()

    if (fileCount == "0") {
        steps.echo "🧹 Folder empty — deleting: ${versionPath}"
        steps.sh "rm -rf '${versionPath}'"
    } else {
        steps.echo "📂 Folder not empty — skip delete: ${versionPath}"
    }

    // ✅ Stop pipeline
    steps.error("Stopping pipeline. Required binary missing: ${fileName}")
    return false
}
