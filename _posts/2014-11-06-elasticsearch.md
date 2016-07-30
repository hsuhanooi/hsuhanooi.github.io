---
layout: post
title: 11 Tips to Optimize Elasticsearch
excerpt: I've been using Elasticsearch for a few months now and I was surprised at how little documentation there was about how to improve Elasticsearch performance. Here are 11 tips.
---

First let me say that using Elasticsearch has been amazing. It was really easy to set up and get rolling and the fact that it's just Lucene underneath is a huge plus for me since I've done some work with it in the past.

However I've been using Elasticsearch for a few months now and I was surprised at how little documentation there was about how to improve Elasticsearch performance. When I first started with Elasticsearch I fought average query times of 1.8s or more and a 90th percentile at 2.5s. But after lots of trial and error and google searching I was able to cut the 90th percentile to 200ms and an average of 70ms. Below I've listed the 11 most helpful things I did to improve performance (for Elasticsearch 1.3.2).

## 1. Make sure Elasticsearch JVM isn't swapping out of memory.
Make sure the Elasticsearch jvm isn't swapping out of memory. You'll see this tip everywhere and it's true. You can run elasticsearch with the mlockall field turned on which will ensure the JVM isn't swapping. You can also disable swap on the machine with sudo swapoff -a but this will only turn it off now but not including restarts. To disable swap on restart make sure you remove the swap keyword from /etc/fstab.

## 2. Set the `cutoff_frequency`
Make sure you set the `cutoff_frequency` parameter on queries with common terms otherwise you get really slow queries for queries with a common term. I was indexing titles which had lots of common words like "the, and, this, 2" so Elasticsearch was returning 100K or more results per query. Scoring that many results really slowed it down and pegged the CPU. Setting the cutoff_frequency to 0.001 cut my 90th percentile in half.  <http://www.elasticsearch.org/guide/en/elasticsearch/reference/current/query-dsl-match-query.html>

## 3. Use bool filters
Make sure you use bool filters instead of and/or filters so elasticsearch can cache the filter fields using bitsets. <http://www.elasticsearch.org/blog/all-about-elasticsearch-filter-bitsets/>.

## 4. Turn on caching explicitly for bool filters
Caching has to be turned on explicitly for bool filters. See bottom of <http://www.elasticsearch.org/guide/en/elasticsearch/reference/current/query-dsl-bool-filter.html>. In the query itself in the bool filter set _cache to true.

## 5. Stripe your data across multiple drives
If you have multiple drives it's really easy to stripe your data across multiple drives and Elasticsearch will write to the disk with the most space left.

## 6. Optimize the number of segments
More segments means more segments to search through and slower queries. The merge policy controls how segments are merged during indexing which for batch loading can be non-optimal so we run optimize segments to merge more segments together in batch after we are finished indexing. The downside is the cluster is very slow/unavailable during optimization as all nodes are merging to the designated number of segments. For me merging down from 30+ segments to 5 on all nodes took 10 mins with 150GB of index data and 600M documents. This had an impact on cutting down the 90th percentile for querying. Also worth noting is the more shards you have, the more segments you'll have which in turn causes optimizing segments to take longer.

## 7. Increase the number of shards
Since we are using custom routing the query should only ever hit 1 shard. So if the shard is smaller (and a shard is just a self-contained lucene index) the query should be faster. 

The Elasticsearch blog also recommends splitting big shards into its own index for more grained control which I also found useful. Unbalanced shards cause queries to larger shards to be much slower than smaller shards. If you aren't using custom routing this is even worse since a query that comes in can query multiple shards and the query must wait for all queries to return. 

## 8. Pre-query filter instead of post filter
Adjusting the filter field to ensure it uses a pre-query filter instead of a post filter. A post filter will return all the results and then slice it down after the query runs whereas a pre-query filter will immediately disregard results which don't meet the filters. A post filter will still cause records that don't match the filter to be scored which is slow.

## 9. Increase the size of ther filter cache
Our use case had very filter heavy queries which was filling up the filter cache. Everybody always talks about the field data cache but for us we had issues with filter cache evictions. I increased the size of the filter cache from 10% to 20% and that reduced evictions by half.

## 10. Use `query_and_fetch` instead of `query_then_fetch`
If you are using smart routing you should use `query_and_fetch` and not the default `query_then_fetch`. `query_then_fetch` makes 2 roundtrips. One for the query and finds all the documents per shard. The second fetches the data. `query_and_fetch` only sends 1 roundtrip and combines both query and fetch into one round trip. This is especially helpful if your Elasticsearch nodes aren't within the same rack or have high latency between nodes.

## Ensure all nodes have about the same storage size.
This is less of an optimization as it is making your devops life easier. Having a single node with a smaller size and using disk-based shard allocation produced ops nightmares for me. The cluster continuously tried to reallocate shards on and off the nodes with a smaller storage space. Later on I found out you can tune the reallocation process using disk based watermarking and tuning how strict the cluster should be about evening out shards. <http://www.elasticsearch.org/guide/en/elasticsearch/reference/current/index-modules-allocation.html>. 
