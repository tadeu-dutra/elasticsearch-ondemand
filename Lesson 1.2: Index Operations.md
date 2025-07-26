# 1.2 Index Operations

In this lab, you will learn how to get data in, create an index, and create a data view by performing the following tasks:

    - From the Console, write, index, retrieve and delete JSON documents, and delete an index
    - Use the file upload mechanism in Kibana to import blogs
    - From Stack Management, create a data view

## Write JSON Documents
NOTE: Notice that the id fields defined inside the documents are just a normal fields like any others. The actual document _id is defined in the request URL when you index the documents.

### Document 1

REQUEST:

```
{
PUT my_blogs/_doc/1
{
  "id": "1",
  "title": "Better query execution",
  "category": "Engineering",
  "date":"July 15, 2015",
  "author":{
    "first_name": "Adrien",
    "last_name": "Grand",
    "company": "Elastic"
  }
}
```

RESPONSE:

```
{
  "_index": "my_blogs",
  "_id": "1",
  "_version": 1,
  "result": "created",
  "_shards": {
    "total": 2,
    "successful": 1,
    "failed": 0
  },
  "_seq_no": 0,
  "_primary_term": 1
}
```

### Document 2

REQUEST:

```
PUT my_blogs/_doc/2
{
  "id": "2",
  "title": "The Story of Sense",
  "date":"May 28, 2015",
  "author":{
    "first_name": "Boaz",
    "last_name": "Leskes"
  }
}
```

RESPONSE:

```
{
  "_index": "my_blogs",
  "_id": "2",
  "_version": 1,
  "result": "created",
  "_shards": {
    "total": 2,
    "successful": 2,
    "failed": 0
  },
  "_seq_no": 1,
  "_primary_term": 1
}
```
NOTE: The index operation can also be executed without specifying the id. In such a case, you use a POST instead of a PUT and an id will be generated automatically.

Index the following document without an id, making sure you use POST, and check the id in the response:

REQUEST:

```
POST my_blogs/_doc/
{
  "id": "57",
  "title": "Phrase Queries: a world without Stopwords",
  "date":"March 7, 2016",
  "category": "Engineering",
  "author":{
    "first_name": "Gabriel",
    "last_name": "Moskovicz"
  }
}
```

RESPONSE:

```
{
  "_index": "my_blogs",
  "_id": "HjSFRJgB3t17zvctnSSl",
  "_version": 1,
  "result": "created",
  "_shards": {
    "total": 2,
    "successful": 2,
    "failed": 0
  },
  "_seq_no": 3,
  "_primary_term": 1
}
```

Use the Elasticsearch Get API to retrieve the document with an _id of 1 from the my_blogs index.:

REQUEST:

```
GET my_blogs/_doc/1
```

RESPONSE:

```
{
  "_index": "my_blogs",
  "_id": "1",
  "_version": 1,
  "_seq_no": 0,
  "_primary_term": 1,
  "found": true,
  "_source": {
    "id": "1",
    "title": "Better query execution",
    "category": "Engineering",
    "date": "July 15, 2015",
    "author": {
      "first_name": "Adrien",
      "last_name": "Grand",
      "company": "Elastic"
    }
  }
}
```

DELETE the document with the _id of 1 from the my_blogs index. Verify it was deleted by trying to GET it again:

REQUEST:

```
DELETE my_blogs/_doc/1
```

RESPONSE:

```
{
  "_index": "my_blogs",
  "_id": "1",
  "_version": 3,
  "result": "deleted",
  "_shards": {
    "total": 2,
    "successful": 2,
    "failed": 0
  },
  "_seq_no": 6,
  "_primary_term": 1
}
```

REQUEST:

```
GET my_blogs/_doc/1
```

RESPONSE:

```
{
  "_index": "my_blogs",
  "_id": "1",
  "found": false
}
```

DELETE the my_blogs index:

```
DELETE my_blogs
```

```
{
  "acknowledged": true
}
```

# Summary

In this lab, you learned how to get data in, create an index, and create a data view by performing the following tasks:

    From the Console, write, index, retrieve and delete JSON documents, and delete an index
    Use the file upload mechanism in Kibana to import blogs
    From Stack Management, create a data view


# Review

- Static data is characterized by slow growth, some updates to existing records, and generally being used for backing search-based applications.
- The Bulk API allows a large number of documents to be ingested, updated, and even deleted in a single POST.
- What happens if you index a document using a document ID and the _id already exists in the index? The document will be updated, and the version will be incremented by 1.
