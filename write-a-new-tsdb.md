# Write a new TSDB

TL;DR this is just a plan for my WIP projects

It's being 3 years since I wrote [Introduction to Time Series Database](https://blog.dongyueweb.com/introduction_to_time_series_database.html).
After that I graduated and went to work on container related things (hi cloud native) and didn't pay much attention to TSDB.
Now I got a long break and decided to revive my old projects like [libtsdb](https://github.com/libtsdb) and [xephon](https://github.com/xephon).
So I decided to write a high level overview of what I am planning to do and clear my thoughts along the way.
One thing I learned after working for 2 years is that if you can't write a design doc that explains what and why before you start,
you are likely going to fell into the rabbit hole of implementation details and end up wasting time and have little to write (on your perf review).

As usual I started with doing survey and revived [awesome time series database](https://xephonhq.github.io/awesome-time-series-database/).
First thing I realized is there are many TSDB project dead or no longer under active development (especially those build on top of Cassandra).
Second is there are more prometheus (compatible) backends, e.g. thanos, m3db, victoriametrics.
Third is there are still new companies building open source TSDB.
Forth is people are still running benchmarks (against each other) and there are even things like [outfluxdata](https://www.outfluxdata.com/).
However many core things remain the same and I feel there are still many things I want to try.

## What is time series data

The most popular data model is a series of timestamp and scalar value pairs tagged with string key value pairs.
Multi dimension means there are multiple key value pairs instead of just a name.

```text
series {
    // tags == dimensions
    tags {
        name: cpu.0
        host: foo-bar
        region: us-west
    }
    data [
        {ts: 2020-02-22 22:46, value: 10},
        {ts: 2020-02-22 22:47, value: 11},
    ]
}
```

Another popular format is tabular format the contains time series data where the tags are converted into columns.
NOTE: although influxdb has a tabular format (fields etc.), under the hood it's still the first tagged series model

```text
ts                | value  |  name  | host    |  region  | 
2020-02-22 22:46  |   10   |  cpu.0 | foo-bar |  us-west | 
```

## Ref

- http://www.iamsysadmin.ninja/2017/03/tsdb-on-postgresql-but-why.html