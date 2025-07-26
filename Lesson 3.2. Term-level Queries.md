# 3.2. Term-level Queries

In this lesson, you’ll learn how to define term-level queries that return an exact match of the original string as it occurs in the documents. You will also implement queries to search for numbers, dates, and IPs by creating ranges. Finally, at the end of this lesson, you will perform a sync search to handle long-running searches.


# Review

- The Query DSL enables you to do any kind of query, including Full Text Queries, Term-Level Queries and Combining Querys.
- In an asynchronous search, is_partial is always set to true while the query is being executed.
- In a range query, you can set up open-ended ranges such as “prices less than $50” or dates greater than or equal to January 1st, 2023.” 
