# 4.2: Combining aggregations

In this lesson, you’ll learn how to combine aggregations to answer more complex questions. For example, you can create multiple aggregations in the same search request or define sub-aggregations to create a hierarchical structure of aggregations.



# Review

- You create a date_histogram to group documents by month, and then calculate the moving average of the “price” field over a three-month window. What type of aggregation have you created? A pipeline aggregation, where the output from the histogram is used for the input to the moving average.
- You can combine a date_histogram aggregation (buckets by year) with a terms aggregation (buckets by category.) to build a visualization like this:
  ![q2](https://github.com/user-attachments/assets/19755161-adb3-4cd2-ac58-e9c22835d5d9)
- When metrics are embedded under a bucket as a sub-aggregation, they calculate their value based on the documents residing inside the bucket. (i.e. Metrics can be embedded under a bucket as a sub-aggregation.)
  
