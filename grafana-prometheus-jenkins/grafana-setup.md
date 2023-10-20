# Jenkins Dashboard

## Host Online Check

- `jenkins_node_online_value`:
  - 1: up (green)
  - 0: down (red)

## Time Series Metrics

### Number of Jenkins Executors
- `jenkins_executor_count_value`

### Number in Queue
- `jenkins_queue_size_value`

### Number of Nodes
- `jenkins_node_count_value`

## Individual Stat Panels

### Plugins Active
- `jenkins_plugins_active`

### Inactive Plugins
- `jenkins_plugins_inactive`

### Plugins with Updates
- `jenkins_plugins_withUpdates`

## Set Variables in Dashboard for Dropdown

-- SHOW TAG VALUES FROM job WITH KEY = "owner"
-- SHOW TAG VALUES FROM job WITH KEY = repo WHERE "owner" =~ /^($folder)$/

## Overall Panel
## Successful Build Countsz

-- SELECT count(build_number) FROM "jenkins_data" WHERE ("project_name" =~ /^(?i)$job$/ AND "project_path" =~ /.*(?i)$folder.*$/) AND ("build_result" = 'SUCCESS' OR "build_result" = 'CompletedSuccess' ) AND $timeFilter

## Failed Build Counts

-- SELECT count(build_number) FROM "jenkins_data" WHERE ("project_name" =~ /^(?i)$job$/ AND "project_path" =~ /.*(?i)$folder.*$/) AND ("build_result" = 'FAILURE' OR "build_result" = 'CompletedError' ) AND $timeFilter

## Aborted Build Counts

-- SELECT count(build_number) FROM "jenkins_data" WHERE ("project_name" =~ /^(?i)$job$/ AND "project_path" =~ /.*(?i)$folder.*$/) AND ("build_result" = 'ABORTED' OR "build_result" = 'Aborted' ) AND $timeFilter

## Unstable Build Counts

-- SELECT count(build_number) FROM "jenkins_data" WHERE ("project_name" =~ /^(?i)$job$/ AND "project_path" =~ /.*(?i)$folder.*$/) AND ("build_result" = 'UNSTABLE' OR "build_result" = 'Unstable' ) AND $timeFilter

## Number of Pipelines Ran

-- SELECT count(DISTINCT project_name) FROM jenkins_data WHERE ("project_name" =~ /^(?i)$job$/ AND "project_path" =~ /.*(?i)$folder.*$/) AND $timeFilter

## Total Number of Builds

-- SELECT count(build_number) FROM "jenkins_data" WHERE ("project_name" =~ /^(?i)$job$/ AND "project_path" =~ /.*(?i)$folder.*$/) AND $timeFilter

## Average Build Time

-- SELECT build_time/1000 FROM jenkins_data WHERE ("project_name" =~ /^(?i)$job$/ AND "project_path" =~ /.*(?i)$folder.*$/) AND $timeFilter

## Latest Build Status

-- SELECT build_result FROM "jenkins_data" WHERE ("project_name" =~ /^(?i)$job$/ AND "project_path" =~ /.*(?i)$folder.*$/) AND $timeFilter ORDER BY time DESC LIMIT 1

## Build Details - Table

-- SELECT "build_exec_time","project_path","build_number","build_causer","build_time","build_result" FROM "jenkins_data" WHERE ("project_name" =~ /^(?i)$job$/ AND "project_path" =~ /.*(?i)$folder.*$/) AND $timeFilter


## Data Links for Build Details Table:
http://your-ip:8080/job/${__data.fields["project_path"]}/${__data.fields["build_number"]}


## Value Map Regex
Find: /(/)/g
Replace with: /job$1