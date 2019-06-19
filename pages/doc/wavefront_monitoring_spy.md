---
title: Getting Data Samples With Spy
tags: [administration, dashboards]
sidebar: doc_sidebar
permalink: wavefront_monitoring_spy.html
summary: Use HTTP endpoints to get samples of the metric points, spans, or ID allocations from your Wavefront instance.
---

Your Wavefront instance includes HTTP `spy` endpoints for sampling the data that your Wavefront instance is currently ingesting.

{% include shared/badge.html content="You need [Direct Data Ingestion permission](permissions_overview.html) to use these HTTP endpoints." %}

**Note:** For an interactive alternative to the `spy` endpoints, use Wavefront Top (`wftop`), which is an [open source tool](https://github.com/wavefrontHQ/wftop).

## Why `spy`?

The Wavefront `spy` endpoints can provide insight into new data that is being ingested by your Wavefront instance. For example, you might analyze `spy` results to:
* Verify that your Wavefront instance is ingesting the data points that you expect.
* Troubleshoot a sudden change in the rate at which new data is ingested. 

Wavefront supports the `spy` endpoints shown in the following table:

<table width="100%">
<tbody>
<thead>
<tr><th markdown="span" width="45%">Spy Endpoint</th><th width="55%">Description</th></tr>
</thead>
<tr><td markdown="span">`https://<cluster>.wavefront.com/api/spy/points`</td>
<td markdown="span">[Gets new metric data points](#getting-ingested-metric-points) that are added to existing time series.</td></tr>
<tr><td markdown="span">`https://<cluster>.wavefront.com/api/spy/spans`</td>
<td markdown="span">[Gets new spans](#getting-ingested-spans) with existing source names and span tags.</td></tr>
<tr><td markdown="span">`https://<cluster>.wavefront.com/api/spy/ids`</td>
<td markdown="span">[Gets newly allocated IDs](#getting-new-id-assignments) that correspond to new metric names, source names, point tags, or span tags. A new ID generally indicates that a new time series has been introduced.</td></tr>

</tbody>
</table>

Each endpoint displays a header that describes your request, and then lists the results, if any, in close to real time (as soon as they are available). Each returned point, span, or ID is listed on a separate line. 

**Note:** A `spy` endpoint returns a sample of the requested data, and you specify the sample size as an endpoint parameter. Because the endpoint connects to a single Wavefront back-end, the sample is taken from just the data that is ingested on a single shard, even when you request 100% sampling.


## Getting Ingested Metric Points

Your Wavefront instance includes an HTTP endpoint that returns a sampling of the ingested metric points that have specified characteristics. You can use the returned list of points to help you answer questions like:

* Show me some ingested points with metric names that start with the prefix `Cust`.
* How many pps come from hosts with names that start with the prefix `web`?
* What are some points that are tagged with `env=prod`?

### Endpoint and Parameters for Metric Points

To get a sampling of ingested data points, use the following endpoint. Replace `<cluster>` with the name of your Wavefront instance:

  ```https://<cluster>.wavefront.com/api/spy/points``` 

To get a sampling of points with specific characteristics, add one or more of the following parameters:

<table width="100%">
<tbody>
<thead>
<tr><th width="15%">Parameter</th><th width="85%">Description</th></tr>
</thead>
<tr><td markdown="span">metric</td>
<td markdown="span">List a point only if its metric name starts with the specified case-sensitive prefix. <br> E.g., `metric=Cust` matches metrics named `Customer`, `Customers`, `Customer.alerts`, but not `customer`.</td></tr>
<tr><td markdown="span">host</td>
<td>List a point only if its source name starts with the specified case-sensitive prefix. </td></tr>
<tr><td markdown="span">pointTagKey</td>
<td markdown="span">List a point only if it has the specified point tag key. Add this parameter multiple times to specify multiple point tags, e.g., `pointTagKey=env&pointTagKey=datacenter` </td></tr>
<tr><td markdown="span">sampling</td>
<td markdown="span">0 to 1, with 0.01 being 1%.  </td></tr>
</tbody>
</table>


### Example Requests for Metric Points

Suppose you have a Wavefront instance named `ex1`.

<table width="100%">
<tbody>
<thead>
<tr><th width="30%">To List a Sample of<br>These Points</th><th width="70%">Use This Query URL</th></tr>
</thead>
<tr>
<td markdown="span">Ingested points for any metric. </td>
<td><code>http://ex1.wavefront.com/api/spy/points</code>
</td>
</tr>
<tr>
<td markdown="span">Ingested points with metric names that start with `Cust`. </td>
<td><code>http://ex1.wavefront.com/api/spy/points?metric=Cust</code>
</td>
</tr>
<tr>
<td>Ingested points that have point tags named <code>env</code> and <code>loc</code>.</td>
<td><code>http://ex1.wavefront.com/api/spy/points?pointTagKey=env&pointTagKey=loc</code>
</td>
</tr>
<tr>
<td>Ingested points from a source whose name starts with <code>web1</code>.</td>
<td><code>http://ex1.wavefront.com/api/spy/points?host=web1</code>
</td>
</tr>
</tbody>
</table>

## Getting Ingested Spans

Your Wavefront instance includes an HTTP endpoint that returns a sampling of ingested spans with specified characteristics. You can use the returned list of spans to help you answer questions like:

* Show me some ingested spans with names that start with the prefix `order`.
* How many spans-per-second come from hosts with names that start with the prefix `web`?
* What are some spans that are tagged with `cluster` or `shard`?


### Endpoint and Parameters for Spans

To get a sampling of ingested spans, use the following endpoint. Replace `<cluster>` with the name of your Wavefront instance:

  ```https://<cluster>.wavefront.com/api/spy/spans```


To get a sampling of spans with specific characteristics, add one or more of the following parameters:

<table width="100%">
<tbody>
<thead>
<tr><th width="15%">Parameter</th><th width="85%">Description</th></tr>
</thead>
<tr><td markdown="span">name</td>
<td markdown="span">List a span only if its operation name starts with the specified case-sensitive prefix. <br> E.g., `name=orderShirt` matches spans named `orderShirt` and `orderShirts`, but not `OrderShirts`.</td></tr>
<tr><td markdown="span">host</td>
<td>List a span only if the name of its source starts with the specified case-sensitive prefix. </td></tr>
<tr><td markdown="span">spanTagKey</td>
<td markdown="span">List a span only if it has the specified span tag key. Add this parameter multiple times to specify multiple span tags, e.g. `spanTagKey=cluster&spanTagKey=shard` </td></tr>
<tr><td markdown="span">sampling</td>
<td markdown="span">0 to 1, with 0.01 being 1%.  
 </td></tr>
</tbody>
</table>


### Example Requests for Spans

Suppose you have a Wavefront instance named `ex1`.

<table width="100%">
<tbody>
<thead>
<tr><th width="30%">To Get a Sample of <br>These Spans</th><th width="70%">Use This Request</th></tr>
</thead>
<tr>
<td>Ingested spans representing any operation.</td>
<td><code>http://ex1.wavefront.com/api/spy/spans</code>
</td>
</tr>
<tr>
<td>Ingested spans with names that begin with <code>orderShirts</code>.</td>
<td><code>http://ex1.wavefront.com/api/spy/spans?name=orderShirts</code>
</td>
</tr>
<tr>
<td>Ingested spans that have span tags <code>cluster</code> and <code>shard</code>.</td>
<td><code>http://ex1.wavefront.com/api/spy/spans?spanTagKey=cluster&spanTagKey=shard</code>
</td>
</tr>
<tr>
<td>Ingested spans from a host whose name starts with <code>web1</code>.</td>
<td><code>http://ex1.wavefront.com/api/spy/spans?host=web1</code>
</td>
</tr>
</tbody>
</table>


## Getting New ID Assignments

During ingestion, Wavefront assigns an ID to each newly added metric name, span name, source name, and <code>key=value</code> string of a point tag or span tag. A new ID generally indicates that a new time series has been introduced.

Your Wavefront instance includes an HTTP endpoint that provides a window into the current stream of new ID assignments. You can use the returned list of ID assignments to see if the data that is currently being ingested has introduced any metrics, sources, spans, or tags that your Wavefront system hasn't seen yet.

### Endpoint and Parameters for New ID Assignments

To get a list of new ID assignments, use the following endpoint. Replace `<cluster>` with the name of your Wavefront instance: 

  ```https://<cluster>.wavefront.com/api/spy/ids```

To get ID assignments for a specific type of new item, add one or more of the following parameters:

<table width="100%">
<tbody>
<thead>
<tr><th width="15%">Parameter</th><th width="85%">Description</th></tr>
</thead>
<tr><td markdown="span">type</td>
<td>
Type of new items you want to see ID assignments for: 
<ul><li>
METRIC - Metric names
</li>
<li>
SPAN - Span names
</li>
<li>
HOST - Source names
</li>
<li markdown="span">
STRING - Point tags or span tags, represented as a single string containing a unique key-value pair, e.g. `env=prod`, `env=dev`, etc.
</li>
</ul>

</td></tr>
<tr><td markdown="span">name</td>
<td>Case-sensitive prefix for the items that you are interested in. </td></tr>
<tr><td markdown="span">sampling</td>
<td markdown="span">0 to 1, with 0.01 being 1% </td></tr>
</tbody>
</table>



### Example Requests for New IDs

Suppose you have a Wavefront instance named `ex1`.

<table width="100%">
<tbody>
<thead>
<tr><th width="35%">To Get ID Assignments <br>For These Items</th><th width="65%">Use This Request</th></tr>
</thead>
<tr>
<td>All metric names, span names, source names, and tags.</td>
<td><code>http://ex1.wavefront.com/api/spy/ids</code>
</td>
</tr>
<tr>
<td>All metric names.</td>
<td><code>http://ex1.wavefront.com/api/spy/ids?type=METRIC</code>
</td>
</tr>
<tr>
<td>Metric names that start with <code>cpu</code>.</td>
<td><code>http://ex1.wavefront.com/api/spy/ids?type=METRIC&name=cpu</code>
</td>
</tr>
<tr>
<td>Key-value pairs with keys that start with <code>comp</code>. </td>
<td><code>http://ex1.wavefront.com/api/spy/ids?type=STRING&name=comp</code>
</td>
</tr>
<tr>
<td>Key-value pairs of the form <code>loc=palo</code>. </td>
<td><code>http://ex1.wavefront.com/api/spy/ids?type=STRING&name=loc%3Dpalo</code>
</td>
</tr>
<tr>
<td>Source names that start with <code>web1</code>.</td>
<td><code>http://ex1.wavefront.com/api/spy/ids?type=HOST&name=web1</code>
</td>
</tr>
</tbody>
</table>