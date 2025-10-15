#!/usr/bin/env bash
# file: read_json_vars.sh

# Read JSON file
json_file="props.json"

# Loop through keys and values
while IFS="=" read -r key value; do
  # Trim quotes
  value="${value%\"}"
  value="${value#\"}"
  # Create a variable dynamically
  eval "${key}='${value}'"
done < <(jq -r 'to_entries | .[] | "\(.key)=\(.value)"' "$json_file")

# ✅ Now you can use them directly
echo "Artifact Version: $artifactVersion"
echo "Build Number: $buildNumber"
echo "Environment: $environment"
