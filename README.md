#!/bin/bash
set -e

PERIOD_TYPE=$1
FROM_INPUT=$2
TO_INPUT=$3

TODAY=$(date +%Y-%m-%d)

if [[ "$PERIOD_TYPE" == "custom" && -n "$FROM_INPUT" && -n "$TO_INPUT" ]]; then
  FROM_DATE=$FROM_INPUT
  TO_DATE=$TO_INPUT
else
  # default last 2 years
  FROM_DATE=$(date -d "-2 years" +%Y-%m-%d)
  TO_DATE=$TODAY
fi

echo "FROM_DATE=$FROM_DATE" > dates.env
echo "TO_DATE=$TO_DATE" >> dates.env

echo "Using dates: $FROM_DATE → $TO_DATE"
