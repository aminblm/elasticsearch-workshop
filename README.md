
# ElasticSearch Workshop
Hosted by Amine M. Boulouma, contact and questions: [amine.boulouma.com](https://amine.boulouma.com)
- [Learn ElasticSearch with Python in 25 minutes (workshop)](https://www.youtube.com/watch?v=ma3BC8aPBfE&t=479s)
- [Source code](https://github.com/amboulouma/elasticsearch-workshop)
- [ElasticSearch installation](https://www.elastic.co/guide/en/apm/get-started/current/install-elasticsearch.html)
- [ElasticSearch python client](https://elasticsearch-py.readthedocs.io/en/master/)

## Installing python client

```pip install elasticsearch```

### Importing packages


```python
# Import packages

from elasticsearch import Elasticsearch
```

### Configurating ElasticSearch


```python
# Elastic search configuation

es = Elasticsearch(HOST="http://localhost", PORT=9200)
es = Elasticsearch()
```

### Creating an index


```python
# Creating index

es.indices.create(index="first_index")
```




    {'acknowledged': True, 'shards_acknowledged': True, 'index': 'first_index'}




```python
# Looking if the index exists

es.indices.exists(index="first_index")
```




    True



### Deleting an index


```python
# Delete index

es.indices.delete(index="first_index")
```




    {'acknowledged': True}




```python
es.indices.exists(index="first_index")
```




    False



### Inserting and getting data


```python
# Inserting data

doc_1 = {"city": "Paris", "country": "France"}
doc_2 = {"city": "Vienna", "country": "Austria"}
doc_3 = {"city": "London", "country": "England"}

es.index(index="cities", doc_type="places", id=1, body=doc_1)
```




    {'_index': 'cities',
     '_type': 'places',
     '_id': '1',
     '_version': 2,
     'result': 'updated',
     '_shards': {'total': 2, 'successful': 1, 'failed': 0},
     '_seq_no': 6,
     '_primary_term': 1}




```python
es.index(index="cities", doc_type="places", id=2, body=doc_2)
```




    {'_index': 'cities',
     '_type': 'places',
     '_id': '2',
     '_version': 3,
     'result': 'updated',
     '_shards': {'total': 2, 'successful': 1, 'failed': 0},
     '_seq_no': 7,
     '_primary_term': 1}




```python
es.index(index="cities", doc_type="places", id=3, body=doc_3)
```




    {'_index': 'cities',
     '_type': 'places',
     '_id': '3',
     '_version': 4,
     'result': 'updated',
     '_shards': {'total': 2, 'successful': 1, 'failed': 0},
     '_seq_no': 8,
     '_primary_term': 1}




```python
# Getting the data

res = es.get(index="cities", doc_type="places", id=1)

res
```




    {'_index': 'cities',
     '_type': 'places',
     '_id': '1',
     '_version': 2,
     '_seq_no': 6,
     '_primary_term': 1,
     'found': True,
     '_source': {'city': 'Paris', 'country': 'France'}}




```python
# Get the data which is present in the _source key

res["_source"]
```




    {'city': 'Paris', 'country': 'France'}



### Search query and matching documents


```python
# Creating our data

doc_1 = {"sentence":"Hack COVID-19 is amazing!"}
doc_2 = {"sentence":"Hack-Quarantine is stunning!"}

es.index(index="english", doc_type="sentences", id=1, body=doc_1)
es.index(index="english", doc_type="sentences", id=2, body=doc_2)
```




    {'_index': 'english',
     '_type': 'sentences',
     '_id': '2',
     '_version': 9,
     'result': 'updated',
     '_shards': {'total': 2, 'successful': 1, 'failed': 0},
     '_seq_no': 18,
     '_primary_term': 1}




```python
# Creating our query

## Match query 

body = {
    "from":0,
    "size":0,
    "query": {
        "match": {
            "sentence":"Hack"
        }
    }
}

res = es.search(index="english", body=body)
res
```




    {'took': 2,
     'timed_out': False,
     '_shards': {'total': 1, 'successful': 1, 'skipped': 0, 'failed': 0},
     'hits': {'total': {'value': 3, 'relation': 'eq'},
      'max_score': None,
      'hits': []}}




```python
body = {
    "from":0,
    "size":2,
    "query": {
        "match": {
            "sentence":"Hack"
        }
    }
}

res = es.search(index="english", body=body)
res
```




    {'took': 2,
     'timed_out': False,
     '_shards': {'total': 1, 'successful': 1, 'skipped': 0, 'failed': 0},
     'hits': {'total': {'value': 3, 'relation': 'eq'},
      'max_score': 0.13786995,
      'hits': [{'_index': 'english',
        '_type': 'sentences',
        '_id': '2',
        '_score': 0.13786995,
        '_source': {'sentence': 'Hack-Quarantine is stunning!'}},
       {'_index': 'english',
        '_type': 'sentences',
        '_id': '3',
        '_score': 0.13786995,
        '_source': {'sentence': 'Hack nCov is great!'}}]}}




```python
body = {
    "from":0,
    "size":2,
    "query": {
        "match": {
            "sentence":"Hack Quarantine"
        }
    }
}

res = es.search(index="english", body=body)
res
```




    {'took': 3,
     'timed_out': False,
     '_shards': {'total': 1, 'successful': 1, 'skipped': 0, 'failed': 0},
     'hits': {'total': {'value': 3, 'relation': 'eq'},
      'max_score': 1.1505672,
      'hits': [{'_index': 'english',
        '_type': 'sentences',
        '_id': '2',
        '_score': 1.1505672,
        '_source': {'sentence': 'Hack-Quarantine is stunning!'}},
       {'_index': 'english',
        '_type': 'sentences',
        '_id': '3',
        '_score': 0.13786995,
        '_source': {'sentence': 'Hack nCov is great!'}}]}}




```python
# match_phrase 

body = {
    "from":0,
    "size":1,
    "query": {
        "match_phrase": {
            "sentence":"Hack Quarantine"
        }
    }
}

res = es.search(index="english", body=body)
res
```




    {'took': 2,
     'timed_out': False,
     '_shards': {'total': 1, 'successful': 1, 'skipped': 0, 'failed': 0},
     'hits': {'total': {'value': 1, 'relation': 'eq'},
      'max_score': 1.1505672,
      'hits': [{'_index': 'english',
        '_type': 'sentences',
        '_id': '2',
        '_score': 1.1505672,
        '_source': {'sentence': 'Hack-Quarantine is stunning!'}}]}}



### Combining queries


```python
# must, must_not and should
 
body = {
    "from":0,
    "size":2,
    "query": {
        "bool": {
            "must_not": {
                "match": {
                    "sentence":"COVID-19"
                }
            },
            "should": {
                "match": {
                    "sentence": "Hack"
                }
            }
        }
    }
}

res = es.search(index="english", body=body)
res
```




    {'took': 2,
     'timed_out': False,
     '_shards': {'total': 1, 'successful': 1, 'skipped': 0, 'failed': 0},
     'hits': {'total': {'value': 2, 'relation': 'eq'},
      'max_score': 0.13786995,
      'hits': [{'_index': 'english',
        '_type': 'sentences',
        '_id': '2',
        '_score': 0.13786995,
        '_source': {'sentence': 'Hack-Quarantine is stunning!'}},
       {'_index': 'english',
        '_type': 'sentences',
        '_id': '3',
        '_score': 0.13786995,
        '_source': {'sentence': 'Hack nCov is great!'}}]}}



### Regular Expressions Queries


```python
# Updating our data

doc_1 = {"sentence":"Hack COVID-19 is amazing!"}
doc_2 = {"sentence":"Hack-Quarantine is stunning!"}
doc_3 = {"sentence":"Hack nCov is great!"}

es.index(index="english", doc_type="sentences", id=1, body=doc_1)
```




    {'_index': 'english',
     '_type': 'sentences',
     '_id': '1',
     '_version': 9,
     'result': 'updated',
     '_shards': {'total': 2, 'successful': 1, 'failed': 0},
     '_seq_no': 19,
     '_primary_term': 1}




```python
es.index(index="english", doc_type="sentences", id=2, body=doc_2)
```




    {'_index': 'english',
     '_type': 'sentences',
     '_id': '2',
     '_version': 10,
     'result': 'updated',
     '_shards': {'total': 2, 'successful': 1, 'failed': 0},
     '_seq_no': 20,
     '_primary_term': 1}




```python
es.index(index="english", doc_type="sentences", id=3, body=doc_3)
```




    {'_index': 'english',
     '_type': 'sentences',
     '_id': '3',
     '_version': 3,
     'result': 'updated',
     '_shards': {'total': 2, 'successful': 1, 'failed': 0},
     '_seq_no': 21,
     '_primary_term': 1}




```python
# regexp query

body = {
    "from":0,
    "size":3,
    "query": {
        "regexp": {
            "sentence":".*"
        }
    }
}

res = es.search(index="english", body=body)
res
```




    {'took': 5,
     'timed_out': False,
     '_shards': {'total': 1, 'successful': 1, 'skipped': 0, 'failed': 0},
     'hits': {'total': {'value': 3, 'relation': 'eq'},
      'max_score': 1.0,
      'hits': [{'_index': 'english',
        '_type': 'sentences',
        '_id': '1',
        '_score': 1.0,
        '_source': {'sentence': 'Hack COVID-19 is amazing!'}},
       {'_index': 'english',
        '_type': 'sentences',
        '_id': '2',
        '_score': 1.0,
        '_source': {'sentence': 'Hack-Quarantine is stunning!'}},
       {'_index': 'english',
        '_type': 'sentences',
        '_id': '3',
        '_score': 1.0,
        '_source': {'sentence': 'Hack nCov is great!'}}]}}


