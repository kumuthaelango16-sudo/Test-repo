#!/bin/bash

# -------- CONFIG --------
SN_INSTANCE="https://<instance>.service-now.com"
SN_USER="your_user"
SN_PASS="your_password"
CHG_NUMBER="CHG0001234"
# -------------------------

echo "Checking change window for $CHG_NUMBER..."

# Call ServiceNow API
response=$(curl -s -u "$SN_USER:$SN_PASS" \
 "$SN_INSTANCE/api/now/table/change_request?sysparm_query=number=$CHG_NUMBER&sysparm_fields=planned_start_date,planned_end_date")

# Extract the start and end times
start=$(echo "$response" | jq -r '.result[0].planned_start_date')
end=$(echo "$response" | jq -r '.result[0].planned_end_date')

if [[ -z "$start" || -z "$end" || "$start" == "null" ]]; then
  echo "ERROR: Unable to fetch change window for $CHG_NUMBER"
  exit 1
fi

echo "Start Time: $start"
echo "End Time  : $end"

# Convert times to epoch
start_epoch=$(date -d "$start" +%s)
end_epoch=$(date -d "$end" +%s)
now_epoch=$(date +%s)

# Validation logic
if [[ $now_epoch -ge $start_epoch && $now_epoch -le $end_epoch ]]; then
  echo "✔ Current time is WITHIN the change window."
  exit 0
else
  echo "❌ ERROR: Current time is OUTSIDE the change window. Stopping process!"
  exit 1
fi
