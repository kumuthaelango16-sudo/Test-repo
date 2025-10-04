#!/bin/bash
set -e

# Usage: ./zip_artifacts.sh <build_type>
BUILD_TYPE=${1:-maven}
ZIP_NAME="artifact_${BUILD_TYPE}.zip"

# -------------------------------
# Define include & exclude patterns
# -------------------------------
case "$BUILD_TYPE" in
  maven)
    INCLUDE_FILES=("*.yml" "target/*.?ar" "target/libs/*.?ar")
    EXCLUDE_FILES=("**/test-classes/**" "**/*.log" "**/node_modules/**")
    ;;
  node)
    INCLUDE_FILES=("package*.json" "dist/**" "*.yml")
    EXCLUDE_FILES=("**/node_modules/**" "**/*.log" "**/coverage/**")
    ;;
  python)
    INCLUDE_FILES=("*.py" "requirements.txt" "*.yml")
    EXCLUDE_FILES=("**/__pycache__/**" "**/*.log")
    ;;
  gradle)
    INCLUDE_FILES=("build/libs/*.?ar" "*.gradle" "*.yml")
    EXCLUDE_FILES=("**/test-results/**" "**/*.log")
    ;;
  *)
    echo "❌ Unknown build type: $BUILD_TYPE"
    exit 1
    ;;
esac

# -------------------------------
# Build the zip command dynamically
# -------------------------------
echo "🔹 Creating $ZIP_NAME"
echo "   Includes: ${INCLUDE_FILES[*]}"
echo "   Excludes: ${EXCLUDE_FILES[*]}"

# Combine include and exclude
zip -r "$ZIP_NAME" ${INCLUDE_FILES[@]} $(printf ' -x %s' "${EXCLUDE_FILES[@]}") >/dev/null

echo "✅ Zip created: $ZIP_NAME"
