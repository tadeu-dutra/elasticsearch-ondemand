# 4.3: Transforming data

In this lesson, you’ll learn how to perform a transform in Kibana using event-centric data. We will examine both pivot transforms, similar to pivot tables which you may already be used to, and the latest transforms, which gather the newest documents in your index.


# Review

- Transforms and destination indices can be configured to work optimally with your dataset.
- When should you use transforms?
  - To pivot your data from event-centric to entity-centric data
  - To find the most recent documents of a particular grouping
- What transform type can you use to answer the following question: “Which of my servers have been idle during the last 10 mins?”
  - Use Latest to keep track of the most recent activity for each server. Find the server that hasn’t had a new document in the last 10 minutes.
  - NOTE: To address the question of idle servers, you should use Latest to keep track of the most recent activity for each server. This way, you can find the server that hasn’t had a new document in the last 10 minutes.
  
