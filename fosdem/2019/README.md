# Contents
* [Saturday](#saturday)
    1. [Pantheon Doc](#pantheon-doc)
    1. [Layered Extensibility in Open Source](#layered-extensibility-in-open-source)
    1. [What's new in Postgres 11](#postgres-11-news)


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
,..

Open source and open data.

Developer toolkit to extend platform.
Official and community built drivers for web API.
Allow deveopers to overwtite or extend all layers.

## Postgres 11 News

Postgres commnuity consists of Core developers, committers and others.
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