#!/bin/bash
set -e

echo "project,metric,date,value" > master_report.csv

for f in *.csv; do
  if [[ "$f" != "master_report.csv" ]]; then
    tail -n +2 "$f" >> master_report.csv
  fi
done

echo "Master report ready"
