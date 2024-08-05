# Bentom-Binoy

#!/bin/bash

# Configuration
SONAR_HOST_URL="http://your-sonarqube-server"
SONAR_AUTH_TOKEN="your_auth_token"
PROJECT_KEY="your_project_key"
SOURCE_BRANCH="source_branch"
NEW_BRANCH="new_branch"

# Export measures from the source branch
export_measures() {
  curl -u $SONAR_AUTH_TOKEN: "$SONAR_HOST_URL/api/measures/component?component=$PROJECT_KEY&branch=$SOURCE_BRANCH" -o measures.json
}

# Export issues from the source branch
export_issues() {
  curl -u $SONAR_AUTH_TOKEN: "$SONAR_HOST_URL/api/issues/search?componentKeys=$PROJECT_KEY&branch=$SOURCE_BRANCH" -o issues.json
}

# Import measures to the new branch
import_measures() {
  curl -u $SONAR_AUTH_TOKEN: -X POST "$SONAR_HOST_URL/api/measures/component_tree?component=$PROJECT_KEY&branch=$NEW_BRANCH" -d @measures.json --header "Content-Type: application/json"
}

# Import issues to the new branch
import_issues() {
  curl -u $SONAR_AUTH_TOKEN: -X POST "$SONAR_HOST_URL/api/issues/bulk_change" -d @issues.json --header "Content-Type: application/json"
}

# Main script execution
export_measures
export_issues
import_measures
import_issues

echo "Branch $SOURCE_BRANCH exported and imported as $NEW_BRANCH"
