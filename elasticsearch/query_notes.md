## **Mother queries for Elasticsearch**
----------
## Run Elasticsearch/Kibana
```
$ bin/elasticsearch
$ curl http://localhost:9200

$ bin/kibana
```
- localhost:9200 (Elasticsearch)
- localhost:5601 (Kibana)

## Getting Started


Check Cluster health.
```
GET /_cluster/health
```
List indices of cluster.
```
GET /_cat/indices?v
```
- Add `v` to parameter to get descriptive header. (`v` for verbose)
- `_cat` defines the API.

List shards within cluster.
```
GET /_cat/shards?v
```

## Create/Deleting Indices
Create new index named pages
```
PUT /pages
```

Delete pages index
```
DELETE /pages
```
Create new index with no. of shards and replica shards
```
PUT /products
{
  "settings": {
    "number_of_shards": 2,
    "number_of_replicas": 2
  }
}
```
## Indexing Documents
Add document
```
POST /products/_doc
{
  "name": "Coffee Maker",
  "price": 64,
  "in_stock": 10
}
```
Adding document with custom ID
```
PUT /products/_doc/100
{
  "name": "Toaster",
  "price": 49,
  "in_stock": 4
}
```
## Retrieving documents by ID
```
GET /products/_doc/100
```
## Updating Documents/Adding New Field
Updating Document
```
POST /products/_update/100
{
  "doc": {
    "in_stock": 3
  }
}
```
Adding new field
```
POST /products/_update/100
{
  "doc": {
    "tags": ["electronics"]
  }
```
Can also do both adding/updating in one since a field that does not exist is automatically added.

## Scripted Updates

## Reducing the current value of `in_stock` by one

```
POST /products/_update/100
{
  "script": {
    "source": "ctx._source.in_stock--"
  }
}
```

## Assigning an arbitrary value to `in_stock`

```
POST /products/_update/100
{
  "script": {
    "source": "ctx._source.in_stock = 10"
  }
}
```

## Using parameters within scripts

```
POST /products/_update/100
{
  "script": {
    "source": "ctx._source.in_stock -= params.quantity",
    "params": {
      "quantity": 4
    }
  }
}
```

## Conditionally setting the operation to `noop`

```
POST /products/_update/100
{
  "script": {
    "source": """
      if (ctx._source.in_stock == 0) {
        ctx.op = 'noop'; 
      }
      
      ctx._source.in_stock--;
    """
  }
}
```
**where ctx is short for context, accessing content through `source` property.
## Conditionally update a field value

```
POST /products/_update/100
{
  "script": {
    "source": """
      if (ctx._source.in_stock > 0) {
        ctx._source.in_stock--;
      }
    """
  }
}
```

## Conditionally delete a document

```
POST /products/_update/100
{
  "script": {
    "source": """
      if (ctx._source.in_stock < 0) {
        ctx.op = 'delete';
      }
      
      ctx._source.in_stock--;
    """
  }
}
```
