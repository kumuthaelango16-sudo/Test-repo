#!/bin/bash
set -e

FILE=$1

echo "Uploading $FILE to dashboard system..."

# Example
# curl -X POST https://dashboard/api/upload -F file=@$FILE

echo "Upload complete"
