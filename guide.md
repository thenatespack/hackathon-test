# Elasticsearch Guide

A quick guide to running and using the Elasticsearch + Kibana setup in `docker-compose.yml`.

## Setup

Start the stack:

```bash
docker compose up -d
```

Check it is running:

```bash
curl localhost:9200
curl 'localhost:9200/_cluster/health?pretty'
```

- Elasticsearch: http://localhost:9200
- Kibana: http://localhost:5601 (Dev Tools has a console for the requests below)

Stop (keeps data):

```bash
docker compose down
```

Stop and delete all data:

```bash
docker compose down -v
```

> Security is disabled for local development. Don't expose this setup publicly.

## Core Concepts

| Concept | Meaning |
|---|---|
| Index | A collection of documents (like a database table) |
| Document | A JSON object stored in an index |
| Mapping | The schema: field names and types |
| Shard | A slice of an index; the unit of distribution |
| Analyzer | Turns text into searchable tokens |

## Indexing Documents

Create an index with a mapping:

```bash
curl -X PUT localhost:9200/products -H 'Content-Type: application/json' -d '{
  "mappings": {
    "properties": {
      "name":     { "type": "text" },
      "category": { "type": "keyword" },
      "price":    { "type": "float" },
      "created":  { "type": "date" }
    }
  }
}'
```

Add a document (auto-generated ID):

```bash
curl -X POST localhost:9200/products/_doc -H 'Content-Type: application/json' -d '{
  "name": "Wireless Mouse",
  "category": "accessories",
  "price": 24.99,
  "created": "2026-11-05"
}'
```

Add or replace a document with a specific ID:

```bash
curl -X PUT localhost:9200/products/_doc/1 -H 'Content-Type: application/json' -d '{
  "name": "Mechanical Keyboard", "category": "accessories", "price": 89.0, "created": "2026-11-05"
}'
```

Bulk index (newline-delimited JSON, must end with a newline):

```bash
curl -X POST localhost:9200/_bulk -H 'Content-Type: application/x-ndjson' --data-binary $'{"index":{"_index":"products","_id":"2"}}\n{"name":"USB-C Hub","category":"accessories","price":39.5,"created":"2026-11-05"}\n'
```

`text` fields are analyzed for full-text search. `keyword` fields are stored as-is, for filtering, sorting and aggregations.

## Searching

Get a document by ID:

```bash
curl localhost:9200/products/_doc/1
```

Full-text search:

```bash
curl -X GET localhost:9200/products/_search -H 'Content-Type: application/json' -d '{
  "query": { "match": { "name": "wireless mouse" } }
}'
```

Filter and combine conditions:

```bash
curl -X GET localhost:9200/products/_search -H 'Content-Type: application/json' -d '{
  "query": {
    "bool": {
      "must":   [ { "match": { "name": "keyboard" } } ],
      "filter": [
        { "term":  { "category": "accessories" } },
        { "range": { "price": { "lte": 100 } } }
      ]
    }
  },
  "sort": [ { "price": "asc" } ],
  "from": 0,
  "size": 10
}'
```

Use `filter` for exact conditions (cached, no scoring) and `must` when relevance scoring matters.

## Aggregations

```bash
curl -X GET localhost:9200/products/_search -H 'Content-Type: application/json' -d '{
  "size": 0,
  "aggs": {
    "by_category": {
      "terms": { "field": "category" },
      "aggs": { "avg_price": { "avg": { "field": "price" } } }
    }
  }
}'
```

## Updating and Deleting

Partial update:

```bash
curl -X POST localhost:9200/products/_update/1 -H 'Content-Type: application/json' -d '{
  "doc": { "price": 79.0 }
}'
```

Delete a document:

```bash
curl -X DELETE localhost:9200/products/_doc/1
```

Delete an index:

```bash
curl -X DELETE localhost:9200/products
```

## Useful Admin Endpoints

```bash
curl 'localhost:9200/_cat/indices?v'          # list indices
curl 'localhost:9200/_cat/health?v'           # cluster health
curl 'localhost:9200/products/_mapping?pretty'
curl localhost:9200/products/_count
```

## Troubleshooting

- **Container exits immediately:** Docker may not have enough memory. Give Docker Desktop at least 4GB.
- **`vm.max_map_count` error (Linux):** run `sudo sysctl -w vm.max_map_count=262144`.
- **Kibana can't connect:** wait for Elasticsearch to become healthy (`docker compose ps`), then check `docker compose logs elasticsearch`.
- **Cluster status yellow:** expected on a single node, because replica shards have nowhere to go. Set `"number_of_replicas": 0` on the index to get green.
- **Port 9200 in use:** change the host port in `docker-compose.yml`, e.g. `"9201:9200"`.

## Going Beyond Local

- Enable security (`xpack.security.enabled=true`) and set passwords.
- Run at least 3 nodes for production, with persistent storage.
- Pin image versions and keep Elasticsearch and Kibana on the same version.
- Docs: https://www.elastic.co/guide/en/elasticsearch/reference/current/index.html
