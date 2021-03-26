# Examples of DSL queries
These are similar examples to the ones found in [This link](https://coralogix.com/log-analytics-blog/42-elasticsearch-query-examples-hands-on-tutorial/).

### Requirements

- Elasticsearch
- Kibana


### setting up Elasticsearch and kibana
In Windows go to elasticsearch folder  ``` elasticsearch\bin\elasticsearch.bat ``` then start kibana ``` kibana\bin\kibana.bat ```

Making a new index:
```
PUT stonks
GET /_cat/indices
```
```
green  open .kibana_task_manager_7.12.0_001 zXjPLXD6ROupehe-p8WEbg 1 0  9 5709 742.5kb 742.5kb
green  open .apm-custom-link                yHrvPT3_S7OywNkg43RWzQ 1 0  0    0    208b    208b
green  open .apm-agent-configuration        s1C-ZjsiQmG56r-svQ87-g 1 0  0    0    208b    208b
yellow open stonks                          G8ujfmlGSVqxynOIM0cFTw 1 1  4    0   8.9kb   8.9kb
green  open .kibana_7.12.0_001              jr-7RtTkS3-sjFUaQ7h5cg 1 0 64    7   4.2mb   4.2mb
green  open .kibana-event-log-7.12.0-000001 dc-tX00VSyu_RTvGGalTIw 1 0  6    0  32.5kb  32.5kb
green  open .tasks                          fp4g582uRnW47s-Fxh3fvQ 1 0  6    0  40.7kb  40.7kb
```

```
```
Filling the data:
```
POST _bulk
{ "index" : { "_index" : "stonks", "_id" : "1" } }
{"id":1,"stonk":"GME", "name":"Game Stop","site":"https://www.gamestop.com","market":"NYSE","price":10.94,"date":"26/03/2021","sentiment":"Positive","sentiment_value":0.7,"mentions":50}
{ "index" : { "_index" : "stonks", "_id" : "2" } }
{"id":2,"stonk":"BB", "name":"Blackberry","site":"https://www.blackberry.com/","market":"NYSE","price":10.94,"date":"26/03/2021","sentiment":"Negative","sentiment_value":0.3,"mentions":10}
{ "index" : { "_index" : "stonks", "_id" : "3" } }
{"id":3,"stonk":"AMC", "name":"AMC Entertainment Holdings","site":"https://www.amctheatres.com/","market":"NYSE","price":10.94,"date":"26/03/2021","sentiment":"Neutral","sentiment_value":0.5,"mentions":30}
```
```
{
  "took" : 30,
  "errors" : false,
  "items" : [
    {
      "index" : {
        "_index" : "stonks",
        "_type" : "_doc",
        "_id" : "1",
        "_version" : 1,
        "result" : "created",
        "_shards" : {
          "total" : 2,
          "successful" : 1,
          "failed" : 0
        },
        "_seq_no" : 0,
        "_primary_term" : 1,
        "status" : 201
      }
    },
    {
      "index" : {
        "_index" : "stonks",
        "_type" : "_doc",
        "_id" : "2",
        "_version" : 1,
        "result" : "created",
        "_shards" : {
          "total" : 2,
          "successful" : 1,
          "failed" : 0
        },
        "_seq_no" : 1,
        "_primary_term" : 1,
        "status" : 201
      }
    },
    {
      "index" : {
        "_index" : "stonks",
        "_type" : "_doc",
        "_id" : "3",
        "_version" : 1,
        "result" : "created",
        "_shards" : {
          "total" : 2,
          "successful" : 1,
          "failed" : 0
        },
        "_seq_no" : 2,
        "_primary_term" : 1,
        "status" : 201
      }
    }
  ]
}

```
Match query examples:
```
POST stonks/_search
{
  "query": {
    "match": {
      "stonk": {
        "query" : "AMC"
      }
    }
  }
}
```
```
{
  "took" : 0,
  "timed_out" : false,
  "_shards" : {
    "total" : 1,
    "successful" : 1,
    "skipped" : 0,
    "failed" : 0
  },
  "hits" : {
    "total" : {
      "value" : 1,
      "relation" : "eq"
    },
    "max_score" : 0.9808291,
    "hits" : [
      {
        "_index" : "stonks",
        "_type" : "_doc",
        "_id" : "3",
        "_score" : 0.9808291,
        "_source" : {
          "id" : 3,
          "stonk" : "AMC",
          "name" : "AMC Entertainment Holdings",
          "site" : "https://www.amctheatres.com/",
          "market" : "NYSE",
          "price" : 10.94,
          "date" : "26/03/2021",
          "sentiment" : "Neutral",
          "sentiment_value" : 0.5,
          "mentions" : 30
        }
      }
    ]
  }
}
```
```
POST stonks/_search
{
  "query": {
    "match": {
      "name": {
        "query" : "AMC Entertainment",
        "operator" : "AND"
      }
    }
  }
}
```
```
{
  "took" : 0,
  "timed_out" : false,
  "_shards" : {
    "total" : 1,
    "successful" : 1,
    "skipped" : 0,
    "failed" : 0
  },
  "hits" : {
    "total" : {
      "value" : 1,
      "relation" : "eq"
    },
    "max_score" : 1.6285465,
    "hits" : [
      {
        "_index" : "stonks",
        "_type" : "_doc",
        "_id" : "3",
        "_score" : 1.6285465,
        "_source" : {
          "id" : 3,
          "stonk" : "AMC",
          "name" : "AMC Entertainment Holdings",
          "site" : "https://www.amctheatres.com/",
          "market" : "NYSE",
          "price" : 10.94,
          "date" : "26/03/2021",
          "sentiment" : "Neutral",
          "sentiment_value" : 0.5,
          "mentions" : 30
        }
      }
    ]
  }
}
```
```
POST stonks/_search
{
  "query": {
    "match": {
      "name": {
        "query" : "Game Stop",
        "minimum_should_match": 2
      }
    }
  }
}
```
```
{
  "took" : 0,
  "timed_out" : false,
  "_shards" : {
    "total" : 1,
    "successful" : 1,
    "skipped" : 0,
    "failed" : 0
  },
  "hits" : {
    "total" : {
      "value" : 1,
      "relation" : "eq"
    },
    "max_score" : 1.9616582,
    "hits" : [
      {
        "_index" : "stonks",
        "_type" : "_doc",
        "_id" : "1",
        "_score" : 1.9616582,
        "_source" : {
          "id" : 1,
          "stonk" : "GME",
          "name" : "Game Stop",
          "site" : "https://www.gamestop.com",
          "market" : "NYSE",
          "price" : 10.94,
          "date" : "26/03/2021",
          "sentiment" : "Positive",
          "sentiment_value" : 0.7,
          "mentions" : 50
        }
      }
    ]
  }
}
```

Term query examples:
```
POST stonks/_search
{
  "query": {
    "term": {
      "sentiment": "positive"
    }
  }
}
```
```
{
  "took" : 0,
  "timed_out" : false,
  "_shards" : {
    "total" : 1,
    "successful" : 1,
    "skipped" : 0,
    "failed" : 0
  },
  "hits" : {
    "total" : {
      "value" : 1,
      "relation" : "eq"
    },
    "max_score" : 0.9808291,
    "hits" : [
      {
        "_index" : "stonks",
        "_type" : "_doc",
        "_id" : "1",
        "_score" : 0.9808291,
        "_source" : {
          "id" : 1,
          "stonk" : "GME",
          "name" : "Game Stop",
          "site" : "https://www.gamestop.com",
          "market" : "NYSE",
          "price" : 10.94,
          "date" : "26/03/2021",
          "sentiment" : "Positive",
          "sentiment_value" : 0.7,
          "mentions" : 50
        }
      }
    ]
  }
}
```
```
POST stonks/_search
{
  "query": {
    "terms": {
      "sentiment": [
        "positive",
        "neutral"
      ]
    }
  }
}
```
```
{
  "took" : 0,
  "timed_out" : false,
  "_shards" : {
    "total" : 1,
    "successful" : 1,
    "skipped" : 0,
    "failed" : 0
  },
  "hits" : {
    "total" : {
      "value" : 2,
      "relation" : "eq"
    },
    "max_score" : 1.0,
    "hits" : [
      {
        "_index" : "stonks",
        "_type" : "_doc",
        "_id" : "1",
        "_score" : 1.0,
        "_source" : {
          "id" : 1,
          "stonk" : "GME",
          "name" : "Game Stop",
          "site" : "https://www.gamestop.com",
          "market" : "NYSE",
          "price" : 10.94,
          "date" : "26/03/2021",
          "sentiment" : "Positive",
          "sentiment_value" : 0.7,
          "mentions" : 50
        }
      },
      {
        "_index" : "stonks",
        "_type" : "_doc",
        "_id" : "3",
        "_score" : 1.0,
        "_source" : {
          "id" : 3,
          "stonk" : "AMC",
          "name" : "AMC Entertainment Holdings",
          "site" : "https://www.amctheatres.com/",
          "market" : "NYSE",
          "price" : 10.94,
          "date" : "26/03/2021",
          "sentiment" : "Neutral",
          "sentiment_value" : 0.5,
          "mentions" : 30
        }
      }
    ]
  }
}
```

Adding a new stonk:
```
PUT stonks/_doc/4
{
  "id": 4,
  "stonk":"AIR",
  "name": "AAR Corp",
  "site":"https://www.amctheatres.com/",
  "market":"NYSE",
  "price":43.63,
  "date":"26/03/2021"
}
```
```
{
  "_index" : "stonks",
  "_type" : "_doc",
  "_id" : "4",
  "_version" : 1,
  "result" : "created",
  "_shards" : {
    "total" : 2,
    "successful" : 1,
    "failed" : 0
  },
  "_seq_no" : 3,
  "_primary_term" : 1
}
```
Update stonk prices:
```
POST stonks/_update/1
{
  "doc": {
    "price": 202.5
  }
}
```
```
{
  "_index" : "stonks",
  "_type" : "_doc",
  "_id" : "1",
  "_version" : 2,
  "result" : "updated",
  "_shards" : {
    "total" : 2,
    "successful" : 1,
    "failed" : 0
  },
  "_seq_no" : 4,
  "_primary_term" : 1
}
```
```
POST stonks/_search
{
  "query": {
    "match": {
      "id": {
        "query" : 1
      }
    }
  }
}
```
```
{
  "took" : 651,
  "timed_out" : false,
  "_shards" : {
    "total" : 1,
    "successful" : 1,
    "skipped" : 0,
    "failed" : 0
  },
  "hits" : {
    "total" : {
      "value" : 1,
      "relation" : "eq"
    },
    "max_score" : 1.0,
    "hits" : [
      {
        "_index" : "stonks",
        "_type" : "_doc",
        "_id" : "1",
        "_score" : 1.0,
        "_source" : {
          "id" : 1,
          "stonk" : "GME",
          "name" : "Game Stop",
          "site" : "https://www.gamestop.com",
          "market" : "NYSE",
          "price" : 202.5,
          "date" : "26/03/2021",
          "sentiment" : "Positive",
          "sentiment_value" : 0.7,
          "mentions" : 50
        }
      }
    ]
  }
}
```

```
POST stonks/_update/2
{
  "doc": {
    "price": 9.70
  }
}
```
```
{
  "_index" : "stonks",
  "_type" : "_doc",
  "_id" : "2",
  "_version" : 2,
  "result" : "updated",
  "_shards" : {
    "total" : 2,
    "successful" : 1,
    "failed" : 0
  },
  "_seq_no" : 5,
  "_primary_term" : 1
}
```
```
{
  "took" : 863,
  "timed_out" : false,
  "_shards" : {
    "total" : 1,
    "successful" : 1,
    "skipped" : 0,
    "failed" : 0
  },
  "hits" : {
    "total" : {
      "value" : 1,
      "relation" : "eq"
    },
    "max_score" : 1.0,
    "hits" : [
      {
        "_index" : "stonks",
        "_type" : "_doc",
        "_id" : "2",
        "_score" : 1.0,
        "_source" : {
          "id" : 2,
          "stonk" : "BB",
          "name" : "Blackberry",
          "site" : "https://www.blackberry.com/",
          "market" : "NYSE",
          "price" : 9.7,
          "date" : "26/03/2021",
          "sentiment" : "Negative",
          "sentiment_value" : 0.3,
          "mentions" : 10
        }
      }
    ]
  }
}
```

Exists query examples:
```
GET stonks/_search
{
    "query": {
        "exists": {
            "field": "sentiment"
        }
    }
}
```
```
{
  "took" : 0,
  "timed_out" : false,
  "_shards" : {
    "total" : 1,
    "successful" : 1,
    "skipped" : 0,
    "failed" : 0
  },
  "hits" : {
    "total" : {
      "value" : 3,
      "relation" : "eq"
    },
    "max_score" : 1.0,
    "hits" : [
      {
        "_index" : "stonks",
        "_type" : "_doc",
        "_id" : "3",
        "_score" : 1.0,
        "_source" : {
          "id" : 3,
          "stonk" : "AMC",
          "name" : "AMC Entertainment Holdings",
          "site" : "https://www.amctheatres.com/",
          "market" : "NYSE",
          "price" : 10.94,
          "date" : "26/03/2021",
          "sentiment" : "Neutral",
          "sentiment_value" : 0.5,
          "mentions" : 30
        }
      },
      {
        "_index" : "stonks",
        "_type" : "_doc",
        "_id" : "1",
        "_score" : 1.0,
        "_source" : {
          "id" : 1,
          "stonk" : "GME",
          "name" : "Game Stop",
          "site" : "https://www.gamestop.com",
          "market" : "NYSE",
          "price" : 202.5,
          "date" : "26/03/2021",
          "sentiment" : "Positive",
          "sentiment_value" : 0.7,
          "mentions" : 50
        }
      },
      {
        "_index" : "stonks",
        "_type" : "_doc",
        "_id" : "2",
        "_score" : 1.0,
        "_source" : {
          "id" : 2,
          "stonk" : "BB",
          "name" : "Blackberry",
          "site" : "https://www.blackberry.com/",
          "market" : "NYSE",
          "price" : 9.7,
          "date" : "26/03/2021",
          "sentiment" : "Negative",
          "sentiment_value" : 0.3,
          "mentions" : 10
        }
      }
    ]
  }
}

```
```
GET stonks/_search
{
  "query": {
    "bool": {
      "must_not": [
        {
          "exists": {
            "field": "sentiment"
          }
        }
      ]
    }
  }
}

```
```
{
  "took" : 0,
  "timed_out" : false,
  "_shards" : {
    "total" : 1,
    "successful" : 1,
    "skipped" : 0,
    "failed" : 0
  },
  "hits" : {
    "total" : {
      "value" : 1,
      "relation" : "eq"
    },
    "max_score" : 0.0,
    "hits" : [
      {
        "_index" : "stonks",
        "_type" : "_doc",
        "_id" : "4",
        "_score" : 0.0,
        "_source" : {
          "id" : 4,
          "stonk" : "AIR",
          "name" : "AAR Corp",
          "site" : "https://www.amctheatres.com/",
          "market" : "NYSE",
          "price" : 43.63,
          "date" : "26/03/2021"
        }
      }
    ]
  }
}

```

Range query examples:
```
POST stonks/_search
{
    "query": {
        "range" : {
            "price" : {
                "gte" : 10,
                "lte" : 100
            }
        }
    }
}
```
```
{
  "took" : 0,
  "timed_out" : false,
  "_shards" : {
    "total" : 1,
    "successful" : 1,
    "skipped" : 0,
    "failed" : 0
  },
  "hits" : {
    "total" : {
      "value" : 2,
      "relation" : "eq"
    },
    "max_score" : 1.0,
    "hits" : [
      {
        "_index" : "stonks",
        "_type" : "_doc",
        "_id" : "3",
        "_score" : 1.0,
        "_source" : {
          "id" : 3,
          "stonk" : "AMC",
          "name" : "AMC Entertainment Holdings",
          "site" : "https://www.amctheatres.com/",
          "market" : "NYSE",
          "price" : 10.94,
          "date" : "26/03/2021",
          "sentiment" : "Neutral",
          "sentiment_value" : 0.5,
          "mentions" : 30
        }
      },
      {
        "_index" : "stonks",
        "_type" : "_doc",
        "_id" : "4",
        "_score" : 1.0,
        "_source" : {
          "id" : 4,
          "stonk" : "AIR",
          "name" : "AAR Corp",
          "site" : "https://www.amctheatres.com/",
          "market" : "NYSE",
          "price" : 43.63,
          "date" : "26/03/2021"
        }
      }
    ]
  }
}

```



