---
title: "Implementing Stable API for Apache Airflow"
linkTitle: "Implementing Stable API for Apache Airflow"
author: "Ephraim Anierobi"
twitter: "ephraimbuddy"
github: "ephraimbuddy"
description: "An Outreachy intern's progress report on contributing to Apache Airflow REST API."
tags: ["REST API"]
date: "2020-07-19"
---

My [Outreachy internship](https://outreachy.org) is coming to its ends which is also the best time to look back and
reflect on the progress so far.

The goal of my project is to Extend and Improve the Apache Airflow REST API. In this post,
I will be sharing my progress so far.

We started a bit late implementing the REST API because it took time for the OpenAPI 3.0
specification we were to use for the project to be merged. Thanks to [Kamil](https://github.com/mik-laj),
who paved the way for us to start implementing the REST API endpoints. Below are the endpoints I
implemented and the challenges I encountered, including how I overcame them.

### Implementing The Read-Only Connection Endpoints
The [read-only connection endpoints](https://github.com/apache/airflow/pull/9095) were the first endpoint I implemented. Looking back,
I can see how much I have improved.

I started by implementing the database schema for the Connection table using Marshmallow 2.
We had to use Marshmallow 2 because Flask-AppBuilder was still using it and Flask-AppBuilder
is deeply integrated to Apache Airflow. This means I had to unlearn Marshmallow 3 that I have
 been studying before this realization and thankfully, I started using Marshmallow 2 in no time.

This first PR would have been more difficult than it was had it been there was no reference
endpoint to look at. [Kamil](https://github.com/mik-laj) implemented a draft PR in which I took inspiration from.
Thanks to this, It was easy for me to write the unit tests. It was also in this endpoint that
 I learned using parameterized in unit tests :D.

### Implementing The Read-Only DagRuns Endpoints

This [endpoint](https://github.com/apache/airflow/pull/9153) came with its many challenges, most especially on filtering with `datetimes`.
This was because the connexion library we were using to build the REST API was not validating
date-time format in OpenAPI 3.0 specification. This I would later find out, was intentional.
Connexion dropped `strict-rfc3339` because of the later licence which is not compatible with
Apache 2.0 licence.

I implemented a workaround on this, by defining a function called `conn_parse_datetime` in the
API utils module. This was later refactored and thankfully, [Kamil](https://github.com/mik-laj)
 implemented a decorator that allowed us to have cleaner code on the views while using this function.

We later tried using `rfc3339-validator` whose licence is compatible with Apache 2.0 licence but
 later discarded this because with our custom date parser we were able to use duration and
 not just date times.

### Other Endpoints
I implemented some different other endpoints. One peculiar issue I faced was because of Marshmallow 2
not giving error when extra fields are in the request body. I implemented a `validate_unknown`
method on the schema to handle this. Thankfully, Flask-AppBuilder updated to using Marshmallow 3,
we quickly updated Flask-AppBuilder in Apache Airflow and started using Marshmallow 3 too.

Here are some PRs I contributed that are related to the REST API:

 1. [Add event log endpoints](https://github.com/apache/airflow/pull/9227)
 2. [Add CRUD endpoints for connection](https://github.com/apache/airflow/pull/9266)
 3. [Add log endpoint](https://github.com/apache/airflow/pull/9331)
 4. [Move limit & offset to kwargs in views plus work on a configurable maximum limit](https://github.com/apache/airflow/pull/9431)
 5. [Update FlaskAppBuilder to v3](https://github.com/apache/airflow/pull/9648)
 6. [Add migration guide from the experimental REST API to the stable REST API](https://github.com/apache/airflow/pull/9771)
### Follow-Ups
There are still lots of works to be done on the REST API including writing helpful documentation.
I still follow up on these and hopefully, we will complete the REST API before the internship ends.

I am very grateful to my mentors, [Jarek](https://github.com/potiuk) and [Kaxil](https://github.com/kaxil) for their
patience with me and for surviving my never-ending questions.

Thanks for reading...