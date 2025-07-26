# 4.1: Metrics and bucket aggregations

In this lesson, you’ll learn about the  different types of aggregations, the basic structure of the request and associated response. You will then analyze data using both metric and bucket aggregations in order to answer many types of questions about your data.


# Review

- Is date_histogram a bucket, metric, or pipeline aggregation? Bucket. The date_histogram groups documents by their date in fixed date range intervals.
- Some buckets are sorted alphabetically. You can sort buckets by a metric value in a sub-aggregation.
- Terms aggregation creates buckets for each unique value of the field. It can be used to put logging events into buckets by log level (“error”, “warn”, “info”, etc.)
