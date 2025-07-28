# 7.2: Data streams

In this lesson, you’ll learn how data streams provide an additional layer of abstraction to simplify and separate time-series data coming from many data sources.

In this lab, you will learn about data streams by performing the following tasks:

    Clone an index template and add a data stream
    Update a component template to add a data stream field
    Roll over the data stream and index a document into the data stream
    View the mappings for the backing indices
    Add an integration to automatically create a data stream



# Review

- When _bulk writing to a data stream, you should use the create action. Data streams are designed to only create, never update.
- Index template to use data streams:
    - Include the data_stream object in the create index template API
    - Set “create data stream” to true in the Index Management UI
NOTE: Either of the first two will work since the data_stream object is really a placeholder object and does not require any additional configuration to create data streams.
- Changes should be performed to the Index Template of a data stream’s configuration.
NOTE: Always make changes to your data stream via the index template.  This ensures that new backing indices will inherit the changes as well.
