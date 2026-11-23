# Audit Log Dataset Schema

## Dataset Design

The prototype will keep two related datasets. The raw audit log records one row per backend or database activity event. A second, model-ready dataset aggregates those events into one row per user session. The session is the unit the models will classify as normal or suspicious.

## Raw Event-Level Audit Log

| Field | Type | Meaning |
|---|---|---|
| `event_id` | Integer or UUID | Unique identifier for the audit event. |
| `session_id` | UUID or string | Session that produced the event. |
| `user_id` | String | Simulated account that produced the event. |
| `event_timestamp` | Timezone-aware timestamp | Time the event occurred; store consistently and convert using the test environment's timezone when deriving time features. |
| `operation` | Category | Operation such as `SELECT`, `INSERT`, `UPDATE`, `DELETE`, or `LOGIN`. |
| `table_name` | String, nullable | Table accessed; null when the event has no table access, such as a login attempt. |
| `status` | Category | Whether the operation succeeded or failed. |
| `rows_returned` | Non-negative integer | Number of rows returned by a successful read; zero for events that return no rows. |
| `estimated_bytes_returned` | Non-negative integer | Estimated response data size for the event. |
| `query_duration_ms` | Non-negative number | Duration of the database operation, when applicable. |

The log will contain only the fields needed for this experiment. It will not store passwords, tokens, personal customer data, or full SQL statements.

## Session-Level Model Dataset

Each row represents one session. `session_id`, `user_id`, session start/end timestamps, and scenario labels are retained as identifiers or evaluation metadata; they are not supplied as model features. The initial numeric feature set is:

| Feature | Definition |
|---|---|
| `session_duration_seconds` | Time between the first and last event in the session. |
| `event_count` | Total logged events in the session. |
| `queries_per_hour` | Database operations divided by session duration in hours. |
| `select_count` | Number of successful or attempted read operations. |
| `write_count` | Number of insert, update, or delete operations. |
| `unique_tables_accessed` | Number of distinct tables accessed. |
| `rows_returned_total` | Sum of rows returned in the session. |
| `estimated_bytes_returned_total` | Sum of estimated response bytes in the session. |
| `failed_event_count` | Number of failed events in the session. |
| `failed_event_rate` | Failed events divided by all events in the session. |
| `after_hours_event_count` | Number of events outside the simulated user's configured normal access window. |
| `sensitive_table_access_count` | Number of events accessing a table marked sensitive in the test schema. |
| `unfamiliar_table_access_count` | Number of events accessing a table outside that simulated user's usual table set. |
| `average_query_duration_ms` | Mean query duration for operations with a recorded duration. |

Categorical operation and table names remain in the raw log. Their session-level signals are represented by counts and table-access features above, so the initial model input is numeric. The preprocessing step will handle missing values and scale numerical features as required by each model.

## Ground-Truth Labels

The session dataset will include `is_suspicious` (`0` = normal, `1` = suspicious) and `scenario_type` (`normal`, `high_query_frequency`, `large_extraction`, `unusual_time`, `repeated_failures`, or `sensitive_or_unfamiliar_table`). These columns are for evaluation only and must never be included in the model input.

For the first controlled dataset, each suspicious session will be generated to represent one primary scenario so scenario-level results are easy to interpret. These labels describe generated test behavior; they do not prove malicious intent.

## Initial Scenario Rules

The following rules define the generated ground-truth labels. They are experimental rules for this prototype, not universal security thresholds.

| Scenario | Initial rule for a generated session |
|---|---|
| Normal | Activity stays within the simulated user's usual rate, access hours, and table set; it has no repeated failures or unusually large extraction. |
| High query frequency | Operation rate is more than three times that user's median normal-session rate. |
| Large extraction | Total rows returned or estimated bytes returned is more than three times that user's median normal-session amount. |
| Unusual time | At least one database event occurs outside the user's configured normal access window. The default simulated window is 08:00–20:00 local test time. |
| Repeated failures | The session contains at least five failed access events. |
| Sensitive or unfamiliar table | The session accesses a table marked sensitive or a table outside the user's configured usual table set. |

The baseline medians and user profiles must be computed or configured from normal activity only. The thresholds should be checked during the first data-generation run and documented before model evaluation; any change must be applied consistently and recorded.

## Data Flow

FastAPI and PostgreSQL activity → raw event-level audit log → session aggregation and feature engineering → model-ready session dataset with evaluation labels.
