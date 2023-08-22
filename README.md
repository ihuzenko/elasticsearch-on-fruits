# Up Sandbox Elastic & Kibana

```
docker-compose -f ./docker-compose.yaml -p my-elastic up -d
```

# Index API

### Index - create

```
PUT /fruits
{
  "settings":{
    "index":{
      "number_of_shards": 1, 
      "number_of_replicas": 0
    }
  }, 
  "mappings": {
    "properties":{
       "id":{ "type":"integer" }, 
       "name": {
         "type": "text", 
         "fields": {
           "kw": { "type": "keyword" }
         }
       }, 
       "description": { "type": "text" },
       "colors": { "type": "keyword" }, 
       "weight": {"type": "double"}
    }
  }
}
```

### Index - get

``` 
GET /fruits
```

### Index - list

```
GET /_cat/indices?format=JSON
```

### Index - delete

```
DELETE /fruits
```

### Index - get mapping

```
GET /fruits/_mapping
```

### Index - update mapping

```
PUT /fruits/_mapping
{
  "properties": {
     "created": { "type": "date"}
  }
}

```

### Index - analyze disk usage

```
POST /fruits/_disk_usage?run_expensive_tasks=true
```

### Index - get settings

```
GET /fruits/_settings
```

### Index - update settings

```
PUT /fruits/_settings
{
 "index": {
   "number_of_replicas": 1,
   "refresh_interval": "10s",
   "max_result_window": 10000
 }
}
```

### Index - get statistics

```
GET /fruits/_stats
```

# Document API

### Create Document

```
PUT /fruits/_doc/1
{ 
  "id": 1,
  "name": "Apple",
  "description": "A round fruit with a red, green, or yellow skin and a white flesh.",
  "colors": ["red", "green", "yellow"], 
  "weight": 150.00
}
```

### Read Document

```
GET /fruits/_doc/1


GET /fruits/_source/1


HEAD /fruits/_doc/1

HEAD /fruits/_doc/2
```

### Update Document

- Scripted
```
POST /fruits/_update/1
{
  "script": {
    "source": "ctx._source.weight += params.count",
    "lang": "painless",
    "params": {"count":1.1}
  }
}
```
- Partial
```
POST /fruits/_update/1
{
  "doc": {
    "name": "Fresh Apple"
  }
}
```

### Delete Document

```
DELETE /fruits/_doc/1
```


# Multi-Document API

### Bulk Operations

```
POST /fruits/_bulk
{"index": {"_id":"1"}}
{"name": "Apple"}
{"index":{"_id":"2"}}
{"name": "Orange"}
{"index":{"_id":"3"}}
{"name": "Cherry"}


POST /fruits/_bulk
{"create": {"_id":"4"}}
{"name": "Mango"}
{"update": {"_id":"2"}}
{"doc":{"name": "Lime"}}
{"delete":{"_id":"3"}}
```
### Bulk index all 

```
DELETE /fruits
```

```
PUT /fruits
{
  "settings":{
    "index":{
      "number_of_shards": 1, 
      "number_of_replicas": 0
    }
  }, 
  "mappings": {
    "properties":{
       "id":{ "type":"integer" }, 
       "name": {
         "type": "text", 
         "fields": {
           "kw": { "type": "keyword" }
         }
       }, 
       "description": { "type": "text" },
       "colors": { "type": "keyword" }, 
       "weight": {"type": "double"}
    }
  }
}
```

```
POST /fruits/_bulk
{"index": {"_id":"1"}}
{"id":1,"name":"Apple","description":"A round fruit with a red, green, or yellow skin and a white flesh.","colors":["red","green","yellow"],"weight":150.00}
{"index": {"_id":"2"}}
{"id":2,"name":"Watermelon","description":"A large, juicy fruit with a green rind and a red or pink flesh.","colors":["green","black"],"weight":5000.00}
{"index": {"_id":"3"}}
{"id":3,"name":"Orange","description":"A round fruit with a tough, shiny, orange skin and a juicy, sweet, orange-colored flesh.","colors":["orange"],"weight":0.00}
{"index": {"_id":"4"}}
{"id":4,"name":"Pear","description":"A sweet, juicy fruit with a green or yellow skin and a white or yellowish flesh.","colors":["green","yellow"],"weight":0.00}
{"index": {"_id":"5"}}
{"id":5,"name":"Cherry","description":"A small, round fruit with a red or black skin and a hard stone inside.","colors":["red","black"],"weight":0.00}
{"index": {"_id":"6"}}
{"id":6,"name":"Strawberry","description":"A small, juicy fruit with a red skin and small seeds on the outside.","colors":["red"],"weight":0.00}
{"index": {"_id":"7"}}
{"id":7,"name":"Nectarine","description":"A juicy fruit with a smooth, fuzzy skin and a sweet, yellow or white flesh.","colors":["yellow","red"],"weight":0.00}
{"index": {"_id":"8"}}
{"id":8,"name":"Grape","description":"A small, juicy fruit with a thin skin and a sweet or sour taste.","colors":["green","red","black","purple"],"weight":0.00}
{"index": {"_id":"9"}}
{"id":9,"name":"Mango","description":"A sweet, juicy fruit with a yellow or green skin and a large, flat seed inside.","colors":["yellow","green"],"weight":0.00}
{"index": {"_id":"10"}}
{"id":10,"name":"Blueberry","description":"A small, round fruit with a blue-black skin and a sweet, juicy flesh.","colors":["blue","black"],"weight":0.00}

```


### Update by Query

```
POST /fruits/_update_by_query
{
  "query": {"match_all": {}}, 
  "script": {
    "source": "ctx._source.remove('weight')",
    "lang": "painless"
  },
  "conflicts": "proceed"
}
```

### Delete by Query

```
POST /fruits/_delete_by_query
{
  "query": {
    "range": {"id":{
      "gt": 3, 
      "lt": 9
    }}
  }
}
```

### Reindex

```
POST /_reindex
{
 "source": { "index": "fruits" },
 "dest": { "index": "fruits-copy" } 
}
```

```
POST /fruits-copy/_search
{
  "query": {"match_all": {}}
}
```

```
GET /fruits-copy/_mapping
```

---



# Test Data

```json
[
  {
    "id": 1,
    "name": "Apple",
    "description": "A round fruit with a red, green, or yellow skin and a white flesh.",
    "colors": [
      "red",
      "green",
      "yellow"
    ],
    "weight": 150.00
  },
  {
    "id": 2,
    "name": "Watermelon",
    "description": "A large, juicy fruit with a green rind and a red or pink flesh.",
    "colors": [
      "green",
      "black"
    ],
    "weight": 5000.00
  },
  {
    "id": 3,
    "name": "Orange",
    "description": "A round fruit with a tough, shiny, orange skin and a juicy, sweet, orange-colored flesh.",
    "colors": [
      "orange"
    ],
    "weight": 0.00
  },
  {
    "id": 4,
    "name": "Pear",
    "description": "A sweet, juicy fruit with a green or yellow skin and a white or yellowish flesh.",
    "colors": [
      "green",
      "yellow"
    ],
    "weight": 0.00
  },
  {
    "id": 5,
    "name": "Cherry",
    "description": "A small, round fruit with a red or black skin and a hard stone inside.",
    "colors": [
      "red",
      "black"
    ],
    "weight": 0.00
  },
  {
    "id": 6,
    "name": "Strawberry",
    "description": "A small, juicy fruit with a red skin and small seeds on the outside.",
    "colors": [
      "red"
    ],
    "weight": 0.00
  },
  {
    "id": 7,
    "name": "Nectarine",
    "description": "A juicy fruit with a smooth, fuzzy skin and a sweet, yellow or white flesh.",
    "colors": [
      "yellow",
      "red"
    ],
    "weight": 0.00
  },
  {
    "id": 8,
    "name": "Grape",
    "description": "A small, juicy fruit with a thin skin and a sweet or sour taste.",
    "colors": [
      "green",
      "red",
      "black",
      "purple"
    ],
    "weight": 0.00
  },
  {
    "id": 9,
    "name": "Mango",
    "description": "A sweet, juicy fruit with a yellow or green skin and a large, flat seed inside.",
    "colors": [
      "yellow",
      "green"
    ],
    "weight": 0.00
  },
  {
    "id": 10,
    "name": "Blueberry",
    "description": "A small, round fruit with a blue-black skin and a sweet, juicy flesh.",
    "colors": [
      "blue",
      "black"
    ],
    "weight": 0.00
  }
]
```