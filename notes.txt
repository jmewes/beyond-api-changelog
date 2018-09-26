#!/bin/bash

CHANGE_LOG_PLACEHOLDER="###CHANGE_LOG_HERE###"
CURRENT_DATE_AND_TIME=$(date '+%Y-%m-%d %H:%M:%S')
CHANGE_LOG_HEADING="== ${CURRENT_DATE_AND_TIME}"

CHANGE_LOG_TABLE_ROWS=$(cat diff.json | jq -r '.[] | .[] | "|" + .ruleId + "\n" + "|" + .message + "\n"')
CHANGE_LOG_TABLE=$(cat <<EOF
|===
|Type |Description

${CHANGE_LOG_TABLE_ROWS}
|===
EOF
)

CHANGE_LOG="\n${CHANGE_LOG_HEADING}\n\n${CHANGE_LOG_TABLE}\n"
ESCAPED_CHANGE_LOG=$(echo -e "${CHANGE_LOG}" | sed -e 's/[\/&|]/\\&/g')

sed -i "/= Beyond API Changelog/ a ${CHANGE_LOG_PLACEHOLDER}" beyond-api-changelog.adoc
perl -pi -e "s/${CHANGE_LOG_PLACEHOLDER}/${ESCAPED_CHANGE_LOG}/g" beyond-api-changelog.adoc
