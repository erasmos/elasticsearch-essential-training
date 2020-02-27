# elasticsearch-essential-training
A reference to assist working through the LinkedIn Learning Course: [Elasticsearch Essential Training](https://www.linkedin.com/learning/elasticsearch-essential-training)

Unless specified otherwise, all of the following commands should be run within the Kibana Console, accessible
via the "Dev Tools".

## Chapter 2: Setup
### Video 3: "Exploring your cluster"

First we'll get a sense of the overall health of the cluster:
```
GET /_cat/health?v
```

Then, we'll get a list of nodes:
```
GET /_cat/nodes?v
```

We'll get a list of all the indices:
```
GET /_cat/indices?v
```

And create a new one called `sales`:
```
PUT /sales
```

To which we'll add one document:
``` 
PUT /sales/_doc/123
{
  "orderID":"123",
  "orderAmount":"500"
}
```

And then retrieve it:
```
GET /sales/_doc/123
```

We'll then delete our new index:
```
DELETE /sales
```

And confirm that it has been deleted:
```
GET /_cat/indices?v
```

## Chapter 3: "Loading Data"
### Video 1: "Bulk loading data"

We're now going to use `curl` to bulk load the data in the file:  [reqs](data/reqs).

From the command line, within the [data](data) directory:
```
curl -s -H "Content-Type: application/x-ndjson" -XPOST localhost:9200/_bulk --data-binary "@reqs"; echo
```

Now, we'll switch back to the Kibana Console, where we'll first verify that the index was created:

```
GET /my-test
```

And then get one of the documents:
```
GET /my-test/_doc/1
```

Now we'll do a similar bulk import, but from within the Kibana console:

``` 
POST _bulk
{ "index" : { "_index" : "my-test-console", "_id" : "1" } }
{ "col1" : "val1" }
{ "index" : { "_index" : "my-test-console", "_id" : "2" } }
{ "col1" : "val2"}
{ "index" : { "_index" : "my-test-console", "_id" : "3" } }
{ "col1" : "val3" }
```

And we can verify one of the documents in our new index:
```
GET /my-test-console/_doc/1
```


### Video 2: "Loading sample data"

Now we'll load in a different sort of data, from the [accounts.json](data/accounts.json) 
file from which you can a sample document:
``` 
{
   "account_number":1,
   "balance":39225,
   "firstname":"Amber",
   "lastname":"Duke",
   "age":32,
   "gender":"M",
   "address":"880 Holmes Lane",
   "employer":"Pyrami",
   "email":"amberduke@pyrami.com",
   "city":"Brogan",
   "state":"IL"
}
```

As we've done above, we use `curl` to import it, but this time we'll specify the index name - `bank` - in the endpoint
as opposed to within the file itself; note that this index will be automatically created for us; ensure that you're in the [data directory](data).
```
curl -H 'Content-Type: application/x-ndjson' -XPOST 'localhost:9200/bank/_bulk?pretty' --data-binary @accounts.json
```

We can now verify that our `bank` index was created, in at least two ways.

We can see it in the list of indices:
``` 
GET /_cat/indices
```

And we can get a description of it:
```
GET /bank
```

### Video 3: "Setting data types"

Now we'll switch to log messages.

First, expand the file [logs.jsonl.tar.gz](data/logs.jsonl.tar.gz):

``` 
tar xfz logs.jsonl.tar.gz 
```

Here's an example document:

``` 
{
   "@timestamp":"2015-05-18T09:03:25.877Z",
   "ip":"185.124.182.126",
   "extension":"gif",
   "response":"404",
   "geo":{
      "coordinates":{
         "lat":36.518375,
         "lon":-86.05828083
      },
      "src":"PH",
      "dest":"MM",
      "srcdest":"PH:MM"
   },
   "@tags":[
      "success",
      "info"
   ],
   "utc_time":"2015-05-18T09:03:25.877Z",
   "referer":"http://twitter.com/error/william-shepherd",
   "agent":"Mozilla/5.0 (X11; Linux x86_64; rv:6.0a1) Gecko/20110421 Firefox/6.0a1",
   "clientip":"185.124.182.126",
   "bytes":804,
   "host":"motion-media.theacademyofperformingartsandscience.org",
   "request":"/canhaz/gemini-7.gif",
   "url":"https://motion-media.theacademyofperformingartsandscience.org/canhaz/gemini-7.gif",
   "@message":"185.124.182.126 - - [2015-05-18T09:03:25.877Z] \"GET /canhaz/gemini-7.gif HTTP/1.1\" 404 804 \"-\" \"Mozilla/5.0 (X11; Linux x86_64; rv:6.0a1) Gecko/20110421 Firefox/6.0a1\"",
   "spaces":"this   is   a   thing    with lots of     spaces       wwwwoooooo",
   "xss":"<script>console.log(\"xss\")</script>",
   "headings":[
      "<h3>f-i-j-nl-ng</h5>",
      "http://facebook.com/success/lodewijk-van-den-berg"
   ],
   "links":[
      "daniel-tani@facebook.com",
      "http://nytimes.com/security/kathryn-sullivan",
      "www.nytimes.com"
   ],
   "relatedContent":[
      {
         "url":"http://www.laweekly.com/news/cbs-crew-rat-fink-2368032",
         "og:type":"article",
         "og:title":"CBS Crew Rat Fink",
         "og:description":"Near a couple of auto body shops (and a sharp new Space Invader mosaic that we&#039;ll post soon) near Temple and Westmoreland is a CBS wall with a nice Rat ...",
         "og:url":"http://www.laweekly.com/news/cbs-crew-rat-fink-2368032",
         "article:published_time":"2008-01-14T08:05:26-08:00",
         "article:modified_time":"2014-10-28T14:59:52-07:00",
         "article:section":"News",
         "article:tag":"Mark Mauer",
         "og:image":"http://IMAGES1.laweekly.com/imager/cbs-crew-rat-fink/u/original/2430299/img_2049.jpg",
         "og:image:height":"360",
         "og:image:width":"480",
         "og:site_name":"LA Weekly",
         "twitter:title":"CBS Crew Rat Fink",
         "twitter:description":"Near a couple of auto body shops (and a sharp new Space Invader mosaic that we&#039;ll post soon) near Temple and Westmoreland is a CBS wall with a nice Rat ...",
         "twitter:card":"summary",
         "twitter:image":"http://IMAGES1.laweekly.com/imager/cbs-crew-rat-fink/u/original/2430299/img_2049.jpg",
         "twitter:site":"@laweekly"
      },
      {
         "url":"http://www.laweekly.com/news/push-and-retna-in-koreatown-2368043",
         "og:type":"article",
         "og:title":"Push and Retna in Koreatown",
         "og:description":"Yeah, I originally had this posted this morning as Push &amp; Ayer - Sorry. It looked like a Retna piece, but I saw the Ayer in there and thought that must ...",
         "og:url":"http://www.laweekly.com/news/push-and-retna-in-koreatown-2368043",
         "article:published_time":"2008-01-29T07:28:32-08:00",
         "article:modified_time":"2014-10-28T14:59:54-07:00",
         "article:section":"News",
         "article:tag":"Shelley Leopold",
         "og:image":"http://IMAGES1.laweekly.com/imager/push-and-retna-in-koreatown/u/original/2430376/img_3671.jpg",
         "og:image:height":"360",
         "og:image:width":"480",
         "og:site_name":"LA Weekly",
         "twitter:title":"Push and Retna in Koreatown",
         "twitter:description":"Yeah, I originally had this posted this morning as Push &amp; Ayer - Sorry. It looked like a Retna piece, but I saw the Ayer in there and thought that must ...",
         "twitter:card":"summary",
         "twitter:image":"http://IMAGES1.laweekly.com/imager/push-and-retna-in-koreatown/u/original/2430376/img_3671.jpg",
         "twitter:site":"@laweekly"
      },
      {
         "url":"http://www.laweekly.com/news/asylm-ruets-pdb-on-santa-monica-2368012",
         "og:type":"article",
         "og:title":"Asylm, Ruets, PDB on Santa Monica",
         "og:description":"Not a new piece, but a well-hidden gem a little south of Santa Monica Blvd. in an alley off of Heliotrope or Edgemont. I&#039;ve been sitting on this for a w...",
         "og:url":"http://www.laweekly.com/news/asylm-ruets-pdb-on-santa-monica-2368012",
         "article:published_time":"2008-04-22T15:11:15-07:00",
         "article:modified_time":"2014-10-28T14:59:48-07:00",
         "article:section":"News",
         "article:tag":"Culture and Lifestyle",
         "og:image":"http://images1.laweekly.com/imager/asylm-ruets-pdb-on-santa-monica/u/original/2430137/img_5027.jpg",
         "og:image:height":"360",
         "og:image:width":"480",
         "og:site_name":"LA Weekly",
         "twitter:title":"Asylm, Ruets, PDB on Santa Monica",
         "twitter:description":"Not a new piece, but a well-hidden gem a little south of Santa Monica Blvd. in an alley off of Heliotrope or Edgemont. I&#039;ve been sitting on this for a w...",
         "twitter:card":"summary",
         "twitter:image":"http://images1.laweekly.com/imager/asylm-ruets-pdb-on-santa-monica/u/original/2430137/img_5027.jpg",
         "twitter:site":"@laweekly"
      },
      {
         "url":"http://www.laweekly.com/news/laurence-tribe-tangles-with-cbs-and-la-city-hall-2396867",
         "og:type":"article",
         "og:title":"Laurence Tribe Tangles with CBS and L.A. City Hall",
         "og:description":"The United States Court of Appeals for the Ninth Circuit&rsquo;s Courtroom 3 - a miniature auditorium with comfortable, smoked salmon-colored seats - wa...",
         "og:url":"http://www.laweekly.com/news/laurence-tribe-tangles-with-cbs-and-la-city-hall-2396867",
         "article:published_time":"2008-06-04T14:16:10-07:00",
         "article:modified_time":"2014-11-26T14:43:59-08:00",
         "article:section":"News",
         "og:site_name":"LA Weekly",
         "twitter:title":"Laurence Tribe Tangles with CBS and L.A. City Hall",
         "twitter:description":"The United States Court of Appeals for the Ninth Circuit&rsquo;s Courtroom 3 - a miniature auditorium with comfortable, smoked salmon-colored seats - wa...",
         "twitter:card":"summary",
         "twitter:site":"@laweekly"
      }
   ],
   "machine":{
      "os":"win xp",
      "ram":3221225472
   },
   "@version":"1"
}
```


This time, we're going to make three indices, each of which will hold the same document type (a log message),
but one for each of three consecutive days; as well, we're going to configure the mapping so that the location
of the request will be correctly interpreted as a `geo_point`.

``` 
PUT /logstash-2015.05.18
```

``` 
PUT /logstash-2015.05.18/log/_mapping?include_type_name=true
{
      
      "properties":{
         "geo":{
            "properties":{
               "coordinates":{
                  "type":"geo_point"
               }
            }
         }
      }
   
}
```

Note that you can ignore the deprecation warning: the `logs.jsonl` file specifies the index `_type` which is
apparently obsolete.

Now we'll do the same for the two other indices:


``` 
PUT /logstash-2015.05.19
```

``` 
PUT /logstash-2015.05.19/log/_mapping?include_type_name=true
{
      
      "properties":{
         "geo":{
            "properties":{
               "coordinates":{
                  "type":"geo_point"
               }
            }
         }
      }
   
}
```

``` 
PUT /logstash-2015.05.20
``` 

``` 
PUT /logstash-2015.05.20/log/_mapping?include_type_name=true
{
      
      "properties":{
         "geo":{
            "properties":{
               "coordinates":{
                  "type":"geo_point"
               }
            }
         }
      }
   
}
``` 

Let's confirm each of those indices:

```
GET /_cat/indices/logstash-*
```

Note the sizes in the last columns.

And now we can import, again using cURL from within the [data directory](data):

``` 
curl -H 'Content-Type: application/x-ndjson' -XPOST 'localhost:9200/_bulk?pretty' --data-binary @logs.jsonl
```

If this worked, then the sizes should have increased:

```
GET /_cat/indices/logstash-*
```

## Chapter 4: Querying Data

### Video 1: "Simple queries"

We're going to query against the `bank` index which we created above.

Firstly, we'll show all of the documents:

``` 
GET /bank/_search
```

There should be 1000 of them; here is the response I received, but with all
but one of the matching documents removed:

``` 
{
   "took":0,
   "timed_out":false,
   "_shards":{
      "total":1,
      "successful":1,
      "skipped":0,
      "failed":0
   },
   "hits":{
      "total":{
         "value":1000,
         "relation":"eq"
      },
      "max_score":1.0,
      "hits":[
         {
            "_index":"bank",
            "_type":"account",
            "_id":"1",
            "_score":1.0,
            "_source":{
               "account_number":1,
               "balance":39225,
               "firstname":"Amber",
               "lastname":"Duke",
               "age":32,
               "gender":"M",
               "address":"880 Holmes Lane",
               "employer":"Pyrami",
               "email":"amberduke@pyrami.com",
               "city":"Brogan",
               "state":"IL"
            }
         }
        ...
      ]
   }
}
```

But of course, we can filter; for example, let's limit ourselves to accounts in California:

``` 
GET /bank/_search
{
  "query": {
    "match": {
      "state": "CA"
    }
  }
}
```

This should return only 17 records, each of which has a state of `CA`.

We can further filter, by insisting that the account holder be employed by a
company called `Techade`:

``` 
GET /bank/_search
{
  "query": {
    "bool": {
      "must": [
        { "match": {"state": "CA"} },
        { "match": {"employer": "Techade"}}
      ]
    }
  }
}
``` 

In this case, we have only a single match:

```
{
   "_index":"bank",
   "_type":"account",
   "_id":"413",
   "_score":10.552503,
   "_source":{
      "account_number":413,
      "balance":15631,
      "firstname":"Pugh",
      "lastname":"Hamilton",
      "age":39,
      "gender":"F",
      "address":"124 Euclid Avenue",
      "employer":"Techade",
      "email":"pughhamilton@techade.com",
      "city":"Beaulieu",
      "state":"CA"
   }
}
```

We can easily filter the inverse - all accounts where the state is not `CA` and
the employer is not `Techade`:

``` 
GET bank/_search
{
  "query": {
    "bool": {
      "must_not": [
        { "match": {"state": "CA"} },
        { "match": {"employer": "Techade"}}
      ]
    }
  }
}
```

Given that we found only 17 records with a state of `CA`, above, that this query returns 983 (1000 - 17)
makes sense (the `employer` clause is redundant here, as the only such account is in California).

Finally, we demonstrate the ability to `boost` the matches for a particular field, while using the
`should` clause:

``` 

GET bank/_search
{
  "query": {
    "bool": {
      "should": [
        { "match": {"state": "CA"} },
        { "match": {
          "lastname": {
            "query": "Smith",
            "boost": 3
            }
          }
        }
      ]
    }
  }
}
```

There are 18 hits, but notice that not all of the accounts are from California; for example:

``` 
{
   "_index":"bank",
   "_type":"account",
   "_id":"516",
   "_score":19.509869,
   "_source":{
      "account_number":516,
      "balance":44940,
      "firstname":"Roy",
      "lastname":"Smith",
      "age":37,
      "gender":"M",
      "address":"770 Cherry Street",
      "employer":"Parleynet",
      "email":"roysmith@parleynet.com",
      "city":"Carrsville",
      "state":"RI"
   }
}
```

And there are some from California, but without a `lastname` of `Smith`, like this one:

``` 
{
   "_index":"bank",
   "_type":"account",
   "_id":"413",
   "_score":4.046554,
   "_source":{
      "account_number":413,
      "balance":15631,
      "firstname":"Pugh",
      "lastname":"Hamilton",
      "age":39,
      "gender":"F",
      "address":"124 Euclid Avenue",
      "employer":"Techade",
      "email":"pughhamilton@techade.com",
      "city":"Beaulieu",
      "state":"CA"
   }
}
```

In fact, there are no documents which match both criteria.

Finally, note the scores of the above matches; the first - where we
had a match on `lastname` but not `state` scored `19.509869`, whereas
the one where we only matched on `state` gave a score of only `4.046554`; this
is down to the `boost`.

If we remove that boost:

``` 
GET /bank/_search
{
   "query":{
      "bool":{
         "should":[
            {
               "match":{
                  "state":"CA"
               }
            },
            {
               "match":{
                  "lastname":"Smith"
               }
            }
         ]
      }
   }
}
```

We get scores of `6.5032897` and `4.046554`, respectively. Frankly, I don't
know why we score higher on the `lastname` than the `state`, but we can see
the effect of the `boost`: it simply multiplies the original score.

### Video 2: "Term-level queries"

Now we'll introduce a different way of searching - using the `term`:

``` 
GET /bank/_search
{
  "query": {
    "term": {
      "account_number": 516
    }
  }
}
```

This will return a single hit:

``` 
{
   "_index":"bank",
   "_type":"account",
   "_id":"516",
   "_score":1.0,
   "_source":{
      "account_number":516,
      "balance":44940,
      "firstname":"Roy",
      "lastname":"Smith",
      "age":37,
      "gender":"M",
      "address":"770 Cherry Street",
      "employer":"Parleynet",
      "email":"roysmith@parleynet.com",
      "city":"Carrsville",
      "state":"RI"
   }
}
```

Note that the `account_number` is numeric.

Let's try the same approach with the `state` field:

``` 
GET /bank/_search
{
  "query": {
    "term": {
      "state": "RI"
    }
  }
}
```

However, this time we get no matches.

Why?

Because strings are not supported for term searches; we
have to use the `match` type query, as we've already seen:

```
GET /bank/_search
{
  "query": {
    "match": {
      "state": "RI"
    }
  }
}
```

We can use multiple terms:

``` 
GET /bank/_search
{
  "query": {
    "terms": {
      "account_number": [516,851]
    }
  }
}
```

This returns two matches, for those account numbers.

We can also use ranges for term-eligible fields.

For example, for `account_number` between those two, inclusively:

``` 
GET /bank/_search
{
  "query": {
    "range": {
      "account_number": {
        "gte": 516,
        "lte": 851,
        "boost": 2
      }
    }
  }
}
```

This returns `336` hits, all of which have an `account_number`
within that range.

And here we get all accounts where the holder is older than `35`:

``` 
GET /bank/_search
{
  "query": {
    "range": {
      "age": {
        "gt": 35
      }
    }
  }
}
```

### Video 3: "Analysis and tokenization"

Now we'll look at various ways that strings are tokenized.

Notice the sometimes subtle difference in result.

``` 
GET bank/_analyze
{
  "tokenizer" : "standard",
  "text" : "The Moon is Made of Cheese Some Say"
}
```

``` 
GET bank/_analyze
{
  "tokenizer" : "standard",
  "text" : "The Moon-is-Made of Cheese.Some Say$"
}
```

``` 
GET bank/_analyze
{
  "tokenizer" : "letter",
  "text" : "The Moon-is-Made of Cheese.Some Say$"
}
```

``` 
GET bank/_analyze
{
  "tokenizer": "standard",
  "text": "you@example.com login at https://bensullins.com attempt"
}
```

``` 
GET bank/_analyze
{
  "tokenizer": "uax_url_email",
  "text": "you@example.com login at https://bensullins.com attempt"
}
```

Note that we detected two new types: `EMAIL` and `URL`.

Now we'll make a new index, specifying the types of the two fields:

``` 
PUT /idx1
{
   "mappings":{
      "properties":{
         "title":{
            "type":"text",
            "analyzer":"standard"
         },
         "english_title":{
            "type":"text",
            "analyzer":"english"
         }
      }
   }
}
```

We can verify that our requested analyzers were respected:

```
GET /idx1
```

``` 
{
  "idx1" : {
    "aliases" : { },
    "mappings" : {
      "properties" : {
        "english_title" : {
          "type" : "text",
          "analyzer" : "english"
        },
        "title" : {
          "type" : "text",
          "analyzer" : "standard"
        }
      }
    },
    "settings" : {
      "index" : {
        "creation_date" : "1582887868872",
        "number_of_shards" : "1",
        "number_of_replicas" : "1",
        "uuid" : "IRnnUA4bTQGTv2gXdV0vdA",
        "version" : {
          "created" : "7060099"
        },
        "provided_name" : "idx1"
      }
    }
  }
}
```

``` 
GET idx1/_analyze
{
  "field": "title",
  "text": "Bears"
}
```

Note that the token was `bears`.

But if we do the same on the field which is set for English analysis:

``` 
GET idx1/_analyze
{
  "field": "english_title",
  "text": "Bears"
}
```

We get a token of `bear`, which it knows to be the root form.

## Chapter 5: "Analyzing Your Data"
### Video 1: "Basic aggregations"

Here we can get a sense of the distribution of accounts by state:

``` 
GET /bank/_search
{
  "size": 0,
  "aggs": {
    "states": {
      "terms": {
        "field": "state.keyword"
      }
    }
  }
}
```

We can see that there are `30` accounts in Texas (`TX`):

``` 
...
    {
      "key" : "TX",
      "doc_count" : 30
    }
...
```

We can then go further, but asking what the average balance is by state:

From this we learn that the average balance in Texas is `$26,073.30`,
but only `$21,731.52` in Wyoming (`WY`).

``` 
...
    {
      "key" : "TX",
      "doc_count" : 30,
      "avg_bal" : {
        "value" : 26073.3
      }
    }
...
```

``` 
...
    {
      "key" : "WY",
      "doc_count" : 25,
      "avg_bal" : {
        "value" : 21731.52
      }
    }
...
```

Let's now add another breakdown: gender (currently limited to two):

``` 
GET bank/_search
{
  "size": 0,
  "aggs": {
    "states": {
      "terms": {
        "field": "state.keyword"
      },
      "aggs": {
        "avg_bal": {
          "avg": {
            "field": "balance"
          }
        },
        "gender":{
          "terms": {
            "field": "gender.keyword"
          }
        }
      }
    }
  }
}
```

For this result, we learn that there are `17` accounts in Texas owned by a female,
and only `13` owned by a male. 

``` 
...
    {
      "key" : "TX",
      "doc_count" : 30,
      "avg_bal" : {
        "value" : 26073.3
      },
      "gender" : {
        "doc_count_error_upper_bound" : 0,
        "sum_other_doc_count" : 0,
        "buckets" : [
          {
            "key" : "F",
            "doc_count" : 17
          },
          {
            "key" : "M",
            "doc_count" : 13
          }
        ]
      }
    }
...
```

Now, we'll get a breakdown of average balance by gender, as grouped by states:

``` 
GET bank/_search
{
  "size": 0,
  "aggs": {
    "states": {
      "terms": {
        "field": "state.keyword"
      },
      "aggs": {
        "avg_bal": {
          "avg": {
            "field": "balance"
          }
        },
        "gender":{
          "terms": {
            "field": "gender.keyword"
          },
          "aggs": {"avg_bal": {"avg": {"field": "balance"} }
          }
        }
      }
    }
  }
}
```

From this we learn that the average balance of female-owned accounts in Texas
is `$29,746.35` versus that for males: `$21,270.077`.

``` 
...
    {
      "key" : "TX",
      "doc_count" : 30,
      "avg_bal" : {
        "value" : 26073.3
      },
      "gender" : {
        "doc_count_error_upper_bound" : 0,
        "sum_other_doc_count" : 0,
        "buckets" : [
          {
            "key" : "F",
            "doc_count" : 17,
            "avg_bal" : {
              "value" : 29746.352941176472
            }
          },
          {
            "key" : "M",
            "doc_count" : 13,
            "avg_bal" : {
              "value" : 21270.076923076922
            }
          }
        ]
      }
    }
...
```

Finally, we can get generate stats on `balance`:

``` 
GET bank/_search
{
  "size": 0,
  "aggs": {
    "balance-stats": {
      "stats": {
        "field": "balance"
      }
    }
  }
}
```

``` 
...
    "balance-stats" : {
      "count" : 1000,
      "min" : 1011.0,
      "max" : 49989.0,
      "avg" : 25714.837,
      "sum" : 2.5714837E7
    }
...
```

### Video 2: "Filtering aggregations"

Let's first remind ourselves of the distribution by state:

``` 
GET /bank/_search
{
  "size": 0,
  "aggs": {
    "states": {
      "terms": {
        "field": "state.keyword"
      }
    }
  }
}
```

This is the same as the following:

```
GET /bank/_search
{
  "size": 0,
  "query": {
    "match_all": {}
  },
  "aggs": {
    "states": {
      "terms": {
        "field": "state.keyword"
      }
    }
  }
}
```

And now we'll filter to only California:

``` 
GET /bank/_search
{
  "size": 0,
  "query": {
    "match": {
      "state.keyword": "CA"
    }
  },
  "aggs": {
    "states": {
      "terms": {
        "field": "state.keyword"
      }
    }
  }
}
```

This will give us only one state:

``` 
...
  "aggregations" : {
    "states" : {
      "doc_count_error_upper_bound" : 0,
      "sum_other_doc_count" : 0,
      "buckets" : [
        {
          "key" : "CA",
          "doc_count" : 17
        }
      ]
    }
  }
...
```

We can also filter by term:

``` 
GET /bank/_search
{
  "size": 0,
  "query": {
    "bool": {
      "must": [
        {"match": {"state.keyword": "CA"}},
        {"range": {"age": {"gt": 35}}}
      ]
    }
  },
  "aggs": {
    "states": {
      "terms": {
        "field": "state.keyword"
      }
    }
  }
}
```

This will only give us `7` hits (we're suppressing the display
of these hits by setting the `size` to `0`).

We'll now re-introduce our metric of average balance:

``` 
GET /bank/_search
{
  "size": 0,
  "query": {
    "bool": {
      "must": [
        {"match": {"state.keyword": "CA"}},
        {"range": {"age": {"gt": 35}}}
      ]
    }
  },
  "aggs": {
    "states": {
      "terms": {
        "field": "state.keyword"
      },
      "aggs": {"avg_bal": {"avg": {"field": "balance"} }}
    }
  }
}
```

``` 
...
    {
      "key" : "CA",
      "doc_count" : 6,
      "avg_bal" : {
        "value" : 22794.166666666668
      }
    }
...
```

Incidentally, here's an alternative way to generate the same result:

``` 
GET bank/_search
{
  "size": 0,
  "query": {
    "match": {"state.keyword": "CA"}
  },
  "aggs": {
    "over35":{
      "filter": {
        "range": {"age": {"gt": 35}}
      },
    "aggs": {"avg_bal": {"avg": {"field": "balance"} }}
    }
  }
}
```

And finally, we remove our filter, but add a global average balance:

``` 
GET bank/account/_search
{
  "size": 0,
  "aggs": {
    "state_avg": {
      "terms": {
        "field": "state.keyword"
      },
      "aggs": {"avg_bal": {"avg": {"field": "balance"}}}
    },
    "global_avg": {
      "global": {},
      "aggs": {"avg_bal": {"avg": {"field": "balance"}}}
    }
  }
}
```

Which adds:

``` 
...
    "global_avg" : {
      "doc_count" : 1000,
      "avg_bal" : {
        "value" : 25714.837
      }
    }
...
```

### Video 3: "Percentiles and histograms"

We'll see how the balances are distributed across percentiles:

``` 
GET bank/_search
{
  "size": 0,
  "aggs": {
    "pct_balances": {
      "percentiles": {
        "field": "balance",
        "percents": [
          1,
          5,
          25,
          50,
          75,
          95,
          99
        ]
      }
    }
  }
}
```

``` 
...
    "aggregations" : {
        "pct_balances" : {
          "values" : {
            "1.0" : 1455.0,
            "5.0" : 3590.5,
            "25.0" : 13713.214285714286,
            "50.0" : 25999.375,
            "75.0" : 38199.53703703704,
            "95.0" : 47601.5,
            "99.0" : 49347.0
          }
        }
      }
...
```

We can make the rendering of those balances more realistic with a
`High Dynamic Range (HDR)` setting:

``` 
GET bank/_search
{
  "size": 0,
  "aggs": {
    "pct_balances": {
      "percentiles": {
        "field": "balance",
        "percents": [
          1,
          5,
          25,
          50,
          75,
          95,
          99
        ],
        "hdr": {
          "number_of_significant_value_digits": 3
        }
      }
    }
  }
}
```

``` 
...
    "aggregations" : {
        "pct_balances" : {
          "values" : {
            "1.0" : 1447.5,
            "5.0" : 3589.5,
            "25.0" : 13679.5,
            "50.0" : 26015.5,
            "75.0" : 38175.5,
            "95.0" : 47551.5,
            "99.0" : 49343.5
          }
        }
      }
...
```

We can also identify at which percentile specific balances occur:

``` 
GET bank/_search
{
  "size": 0,
  "aggs": {
    "bal_outlier": {
      "percentile_ranks": {
        "field": "balance",
        "values": [35000,50000],
        "hdr": {
          "number_of_significant_value_digits": 3
        }
      }
    }
  }
}
```

``` 
...
    "aggregations" : {
        "bal_outlier" : {
          "values" : {
            "35000.0" : 69.0,
            "50000.0" : 100.0
          }
        }
      }
...
```

And finally, we can create a sort of histogram:

``` 
GET bank/_search
{
  "size": 0,
  "aggs": {
    "bals": {
      "histogram": {
        "field": "balance",
        "interval": 500
      }
    }
  }
}
```

``` 
...
    "aggregations" : {
        "bals" : {
          "buckets" : [
            {
              "key" : 1000.0,
              "doc_count" : 13
            },
            {
              "key" : 1500.0,
              "doc_count" : 6
            }
            ...
            {
               "key" : 49000.0,
               "doc_count" : 11
            },
            {
               "key" : 49500.0,
               "doc_count" : 7
            }
          ]
        }
    }
...
```