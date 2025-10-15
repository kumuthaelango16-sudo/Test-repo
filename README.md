#!/usr/bin/env sh

json_file="props.json"

jq -r 'to_entries | .[] | "\(.key)=\(.value)"' "$json_file" |
while IFS="=" read -r key value; do
  value="${value%\"}"
  value="${value#\"}"
  eval "${key}='${value}'"
done

echo "Artifact Version: $artifactVersion"
echo "Build Number: $buildNumber"
