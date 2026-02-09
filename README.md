
#!/bin/bash
set -e

PROJECT=$1
METRICS=$2
SONAR_URL=$3
TOKEN=$4

source dates.env

mkdir -p cache

CACHE_FILE="cache/${PROJECT}_${FROM_DATE}_${TO_DATE}.json"
OUT_FILE="${PROJECT}.csv"

echo "project,metric,date,value" > $OUT_FILE

if [ -f "$CACHE_FILE" ]; then
  echo "Using cache for $PROJECT"
else
  echo "Calling Sonar API for $PROJECT"

  URL="$SONAR_URL/api/measures/search_history?component=$PROJECT&metrics=$METRICS&from=$FROM_DATE&to=$TO_DATE"

  n=0
  until [ "$n" -ge 3 ]
  do
    curl -s -u $TOKEN: "$URL" > "$CACHE_FILE" && break
    n=$((n+1))
    echo "Retry $n..."
    sleep 5
  done
fi

# Convert JSON → CSV
jq -r --arg P "$PROJECT" '
.measures[] as $m |
$m.history[] |
[$P, $m.metric, .date, .value] | @csv
' "$CACHE_FILE" >> $OUT_FILE

echo "Generated $OUT_FILE"
