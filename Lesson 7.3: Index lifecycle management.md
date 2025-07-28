# 7.3: Index lifecycle management

In this lesson, you’ll learn all about using the Index Lifecycle Management tools, including how to create a policy, and defining various actions to take as your index moves through the various policy phases.

In this lab, you will learn about index lifecycle policies by performing the following tasks:

    Create an index lifecycle policy
    Apply an index lifecycle policy to a data stream
    Manage the indices for time-series data




# Review

- When a hot index rolls over, write requests are automatically sent to the newly created backing index.
- You have 5 phases in ILM: Hot/Warm/Cold/Frozen/Delete.
- If you want an index in the warm phase for 5 days, then have it move to the cold phase, what would you set min_age to in the cold phase? 5 days. To execute this pattern, you’d set rollover in hot to 1 day, and then set the min_age in the cold phase to 5 days.

