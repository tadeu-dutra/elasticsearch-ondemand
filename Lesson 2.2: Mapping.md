# Lesson 2.2: Mapping

In this lesson, you’ll learn about mappings, a way for Elasticsearch to decide how to define fields when indexing and storing data. You will also learn how to define your own custom mappings as well as mapping optimization techniques.



# Review

- Each index will have its own mappings, which lets developers tune each index for a specific use case.
- A mapping cannot be changed. (i.e., You cannot change a field’s data type from integer to long).
- Creating a custom mapping will produce savings in search and index speed, as well as memory and storage requirements. It will not, however, make any changes to the _source.
  - Creating a custom mapping may improve search and index speeds
  - Creating a custom mapping may improve memory and storage requirements
  - Creating a custom mapping doesn't change the _source containing the original JSON object
