# Contents
* [Saturday](#saturday)
    1. [Pantheon Doc](#pantheon-doc)
    1. [Layered Extensibility in Open Source](#layered-extensibility-in-open-source)
    1. [What's new in Postgres 11](#postgres-11-news)
* [Sunday](#sunday)
    1. [Critical PAth Analysis](#critical-path-analysis)
    1. [Observability 101](#observability-101)
    1. [Loki Pormetheus for Logs](#loki-prometheus-for-logs)
    1. [Latency SLOs Done Right](#latency-slos-done-right)
    1. [Conatinerd update](#containerd-update)


# Saturday

## Pantheon Doc
Developed for a Ethereum blockchain client.
Requirements:
* use markdown

* code and doc must be in sync

* reviews and comments
* easy contributions
* usage stats
* automatic ToC
* search

https://docs.pantheon.pegasys.tech

Solution:
* readthedocs.org
* mkdocs and matherial theme
* python makrdown extensions
* google analytics
* writing style guide

Subprojects in readthedocs to manage multiple respositories with docs.
Root of documentation is `docs/`


## Layered Extensibility in Open Source

Mattermost approach to Layered Extensibility in Open Source

Layers:
commands and webhooks
rest api
plugins
...

Open source and open data.

Developer toolkit to extend platform.
Official and community built drivers for web API.
Allow deveopers to overwtite or extend all layers.

## Postgres 11 News

Postgres community consists of Core developers, committers and others.
Every two months there're commit fest when people mostly contribute.
Every stable version is branched and new branch for next release is created.
Project is very conservative.
Commitfest - patches do not get lost, quickly find patches, single place to track

Notable features:

* partitioning

* parallelization: limit clause is pushed to workers
* performance: pg_prewarm
* logical replication, fewer superuser checks more grant based, transaction control in PL procedures
* pgbench, psql quit and exit commands
* logging improvements
* autovacuum improvements
* recovery.conf was integrated in postgresql.conf

Ongoing work:
 * pluggable storage
 * zheap that will eliminate autovacuum
 * database encryption
 * synchronous logical replication
 * autonomous transactions

# Sunday

## Critical Path Analysis

> Jaana Dogan @ Google

What helps the big company:

* code search for all the code base
* build system

CPA discover critical paths, make them reliable and fast, make them debuggable.

How to get there:
   * tracing, events, logs
   * tracing between stacks: code, frameworks, libs, OS, hardware

Challenges:
   * organizational problem, should be a standard
   * people don't know where to start
   * dynamic capabilities are underestimated

## Observability 101

> Richard Hartmann @ qSkills

Observability is a function that helps observe, understand and act

Metrics, logs, traces, dumps

Metrics are the entry point.
Logs are usually the second step, Traces and dumps are to debug individual system components.

Break complexity and make it manageable.

Contract implies a firm commitment.
Avoid data lakes, add metadata when possible.

Standards for monitoring: SNMP, Prometheus -> Open Metrics

## Loki Prometheus for logs

> Tom Wilkie @ Grafana Labs

Alert -> dashboard -> logs -> traces -> fix.

K8s native, supports cloud storage, doesn't index contents.

More for quick logs analysis and not real analytics.

MTail for alerting https://github.com/google/mtail

## Latency SLOs Done Right

> Heinrich Hartmann @ circonus

How many requests were served under Xms?

Latency is important, one of the four golden signals by Google SRE book,

SLO - service level objective.

Percentiles should be coupled with actual metrics, like requests count.

* Method 1:
Count log data

Storing all the logs is expensive.


* Method 2:
Counter Metrics
add a metric that counts how many requests were under $threshold

You need to chose your latency thresholds beforehand.

* Method 3

  HDR Histograms

  Store latency distribution in sparse log linear buckets.
  Saves space, more efficient.

## Containerd update

> Phil Estes @ IBM

KubeConf Seattle deep dive talk on architecture.

containerd added k8s support through CRI plugin

Server plugin architecture, smart client model.

Seems that CRI-O by RedHat will be abandoned.

