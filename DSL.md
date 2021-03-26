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

Update stonk prices:
```
POST stonks/_update/1
{
  "doc": {
    "price": 202.5
  }
}

POST stonks/_update/2
{
  "doc": {
    "price": 9.70
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



