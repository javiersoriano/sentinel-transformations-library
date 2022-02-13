# Filtering at ingestion time

Filtering incoming logs is essential to avoid noise in our telemetry and to keep ingestion costs under control.

In this folder we have two examples on how to achieve filtering: dropping fields or entire rows.

## Dropping fields

This is about removing fields that don't add any value to our security operations. The way to achieve this in transformKql is very simple:

```
source 
| project-away Version, InterfaceId
```

Just using ```project-away``` will avoid ingesting the specified fields into the workspace. Take into account that the column will not disappear, but field will be empty, avoiding the ingestion cost.

## Dropping rows

This is about discarding entire rows (records) when certain conditions are met. Example:

```
source | where Action contains 'REJECT'
```

In this example we're using a table with firewall traffic information, where we just want to keep records where the action taken by the firewall was to reject the traffic. We use ```where``` clause to do that.