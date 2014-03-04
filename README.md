Hands On Lab
============

This repository contains project models for hands on lab sessions about elasticsearch.


Build Status
============

Thanks to cloudbees for the [answers branch build status](https://buildhive.cloudbees.com): [![Build Status](https://buildhive.cloudbees.com/job/elasticsearchfr/job/hands-on/badge/icon)](https://buildhive.cloudbees.com/job/elasticsearchfr/job/hands-on/)


How to use it
=============

Optional
--------

First, you can download the latest version of [Elasticsearch](http://www.elasticsearch.org/download/):

```sh
curl -OL -k http://download.elasticsearch.org/elasticsearch/elasticsearch/elasticsearch-1.0.1.zip
```

Modify `config/elasticsearch.yml` file. Elasticsearch use by default:

* `elasticsearch` as cluster name
* multicast discovery

```properties
# cluster.name: elasticsearch
cluster.name: handson

# discovery.zen.ping.multicast.enabled: false
discovery.zen.ping.multicast.enabled: false
```

You can also download some nice GUI plugins:

* [Marvel Plugin](http://www.elasticsearch.org/overview/marvel/) (free for dev)
* [Kopf Plugin](https://github.com/lmenezes/elasticsearch-kopf/zipball/master)

```sh
bin/plugin -i elasticsearch/marvel/latest
bin/plugin -install lmenezes/elasticsearch-kopf
```



Download the project
--------------------

     git clone https://github.com/elasticsearchfr/hands-on.git

Compile the project
-------------------

     mvn compile

Run tests
---------

     mvn test

Tests should fail as you have to fill blanks!


Slides
======

Slides are available on [Slideshare](http://www.slideshare.net/dadoonet/hands-on-lab-elasticsearch)


Use cases
=========

Test 0: just start a node
-------------------------

When running [NodeTest](https://github.com/elasticsearchfr/hands-on/blob/master/src/test/java/org/elasticsearchfr/handson/ex0/NodeTest.java),
you should bring to live an Elasticsearch node.

When successful, launch an external Elasticsearch node, using the optional download above and look at logs.
You should see that when running test, both nodes see each other.

You can add the following line at the end of NodeTest:

     Thread.sleep(120000);
     
And open in your browser: [http://localhost:9200/_plugin/marvel/](http://localhost:9200/_plugin/marvel/) to see one node, then both nodes and then only one node.
Have a look also at [http://localhost:9200/_plugin/kopf/](http://localhost:9200/_plugin/kopf/)


Test 1: Index/Get and Delete some documents
-------------------------------------------

All tests are in [IndexTest.java](https://github.com/elasticsearchfr/hands-on/blob/master/src/test/java/org/elasticsearchfr/handson/ex1/IndexTest.java).

### Index one Beer - indexOneBeer()

We index one beer in index meal, type beer: Heineken, PALE, 0.33 L, 3 EUROS.
We have defined a Javabean to handle [Beer](https://github.com/elasticsearchfr/hands-on/blob/master/src/test/java/org/elasticsearchfr/handson/beans/Beer.java) documents.

We will:
* use Jackson to generate JSon from object
* index the beer
* get back the generated ID for this document
* ask Elasticsearch to get the document back
* deserialize it from JSon to javabean Beer
* check that sent and indexed objects are equals
* remove the beer document and check that it does not exist anymore


### Index 1000 Beers (or more) - indexOneThousandBeers()

We index 1 000 beers with the Elasticsearch bulk feature.

We will:
* index all beers
* check that there is no failure
* remove all beers

You can increase the number of beer to see how fast is it on your personal computer.


Test 2: Searching for documents
-------------------------------

Before each test we index 1000 beers. After each test, we remove them.

Tests are in [SearchTest.java](https://github.com/elasticsearchfr/hands-on/blob/master/src/test/java/org/elasticsearchfr/handson/ex2/SearchTest.java).

### Match All Query - matchAllSearch()

We search for all documents. We will learn to build a very simple query and see how we can retrieve
a single hit from results.

Even if you build well the query, it will failed. Can you explain why and how to fix it?

### Term Query - termSearch_not_working()

We want to search for term "Heineken" in brand field. We will get 0 hit. Can you explain why?

### Term Query - termSearch()

We fix the previous test. We should get more than 0 beer.

### Match Query - textSearch()

We can search for HEINEKEN or HeiNEken in brand field. We should get more than 0 beer.

What is the main difference between term and match queries?

### Query String Query - queryStringSearch()

We search for HEINEKEN in all documents. 

We can also use here a Lucene syntax.
For example, you can also search for "+heineken -pale" or for "+heineken pale" and see the differences.

### Range Query - rangeSearch()

We build here a query based on beer price. We want to get beers with price between 5 and 10.

### Boolean Query with Match and Range queries - bool_text_and_range_Search()

We build a boolean query to get HEINEKEN beers with price between 5 and 10.

We will check the first 10 hits that they answer to this rule.

### Query with filters - query_and_filter_Search() - Ignored

*This test is disabled by default*. We will talk about it if we have enough time during the lab.

We use the same query as the previous exercise but we want to filter results with at least 1 litter beers.

We will check the first 10 hits that they answer to this rule.

### Search like google - google_Search()

We will ask for the first 100 results of "HEINEKEN pale". Let's see in logger how scoring order 
results.

### Search like google with boost - google_with_boost_Search()

We will ask for the first 100 results of "HEINEKEN pale^3". Let's see in logger how scoring order 
results. We also want to "highlight" some fields: colour and brand to see how it appears in the JSon raw output.

### Multi search - multi_Search()

We want to run two queries at once:

 * The first query is a queryString with "pale". We want only one result.
 * The second query is a matchQuery on "brand" with "HEINEKEN". We want only one result.


### Fuzzy search - fuzzySearch()

We want to build Fuzzy Query and we want to find heinezken (mispelling).


Test 3: Analyzing documents with facets
---------------------------------------

Before each test we index 1000 beers. After each test, we remove them.

Tests are in [FacetTest.java](https://github.com/elasticsearchfr/hands-on/blob/master/src/test/java/org/elasticsearchfr/handson/ex3/FacetTest.java).

### MatchAll Query with Terms Facet on beer brand - brand_termsFacet_matchAllQuery()

We want to count beers by brand. We will create a matchAll query and add a "bybrand" terms facet on field "brand".

We will check that the sum of all counts is equal to 1000.

### Term Query with Terms Facet on beer brand - brand_termsFacet_termQuery()

We want to count beers by brand. We will search for brand equals to "heineken" and add a "bybrand" terms 
facet on field "brand".

We will check that the sum of all counts is less than 1000.

### Term Filter with Terms Facet on beer brand - brand_termsFacet_termFilter() - Ignored

*This test is disabled by default*. We will talk about it if we have enough time during the lab.

We want to count beers by brand. We will search for all beers and apply a term filter on brand to
get only "heineken" beers and we will add a "bybrand" terms facet on field "brand".

We will see that the sum of all counts is 1000 unless we only get less than 1000 results.
Could you explain why?

### Term Filter with Terms Facet on beer brand with filter - brand_termsFacet_withFilter_termFilter() - Ignored

*This test is disabled by default*. We will talk about it if we have enough time during the lab.

We want to count beers by brand. We will search for all beers and apply a term filter on brand to
get only "heineken" beers and we will add a "bybrand" terms facet on field "brand" and we will reduce it
with a the same filter as above.

We will check that the sum of all counts is less than 1000.

### MatchAll Query with Range Facet on beer price - brand_rangeFacet_matchAllQuery()

We want to get some statistics on beer prices. We will create a matchAll query and add a "byprice" range facet on field "price":
* price up to 3
* price between 3 and 6
* price more than 6

We will check that the sum of all counts is equal to 1000. Because all ranges are exclusive.
What will happen if we use the following ranges:
* price up to 3
* price between 2 and 8
* price more than 6


Bonuses
=======

TODO : Let's play with multiple nodes, with sharding and replicas.
And let's monitor it with:

* [Marvel](http://localhost:9200/_plugin/marvel/)
* [Kopt](http://localhost:9200/_plugin/kopf/)

