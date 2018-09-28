# Contents
* [Saturday](#saturday)
    1. [Monitoring legacy Java applications with Prometheus](#monitoring-legacy-java-applications-with-prometheus)
    1. [PostgreSQL replication in 2018](#postgresql-replication-in-2018)
    1. [PostgreSQL and Kubernetes](#postgresql-and-kubernetes)
    1. [Elasticsearch Revolution](#elasticsearch-revolution)
* [Sunday](#sunday)
    1. [Documentation as source code](#documentation-as-source-code)
    1. [Provisioning vs Configuration Management Deployment vs Orchestration](#Provisioning-vs-Configuration-Management-Deployment-vs-Orchestration)
    1. [Kubernetes load balancing for virtual machines](#kubernetes-load-balancing-for-virtual-machines)
    1. [MySQL InnoDB Cluster](#mysql-innodb-cluster)
    1. [Containerd](#containerd)
    1. [Kubernetes Security Practices](#kubernetes-security-practices)
    1. [Container security practices](#container-security-practices)

# Saturday

## Monitoring legacy Java applications with Prometheus
### Logfiles monitoring
logs parsing
tool: https://github.com/google/mtail
grok\_exporter turns logs into metrics
prometheus labels to parse logs
turns logs into metrics

### Blackbox exporter

blackbox exporter calls url and parses response with modules, like http 200
should cover all possible endpoints

### JMX
java visualvm is a JDK tool to connect to JMX
prometheus has parser for jmx xml shit
jmx exporter
polling jmx data may cause significant performance penalty
### DIY java agent

Hooks into classes and collects metrics
promagent.jar java exporter


## PostgreSQL Replication in 2018
Back then postgres had no replication! Not a case now.
Both external and internal solutions for replication.
Replication can be done at different layers:
    - application
    - app in database
    - trigger based
    - database logical
    - database physical
    - OS - DRBD
    - Hardware based replication - SAN

### SAN replication
Block level, transparent, magic inside.
Fails hard.

### Operating system
DRBD the most common, implemented as OS driver, slow.

### Database physical
WAL based, file based.
Synchronous mode (replication):
    - standby nodes in readonly mode
    -  three modes:
        - simple
        - firsthttps://repmgr.org/
        - quorum

**Streaming replication**: very easy to set up, hard to get wrong, it's working,
built in, use a user with `REPLICATION` privilege, `-X stream` with postgres 10 is default, different OSes and platforms are not supported,
replicates everything, can replicate in one direction,
no cluster management, doesn't support (easy) fallback: might be uncommitted data.

**Patroni** tool for replication and failover and cluster management.
https://github.com/zalando/patroni

**repmgr** cluster replication manager (https://repmgr.org/)

**PAF** pacemaker based, manages virtual ip

### Database logical
wal\_level='logical'  
Reconstruct changes on a raw basis. Table level or other partial replication. Might work most of the times but not **all** the time. Foreign keys may be an issue.

Drawbacks:
  - no schema replication
  - doesn't replicate sequences
  - not good for HA replication
  - not complete

Extension: **pglogical** has more capabilities today, was partly integrated in Postgres 10. Does support sequence replication, column and raw based filtering.

### App in database
Easy to break, not easy to work with. Slony, Bucardo, Longdiste.
Multimaster is tricky. Completely transparent multimaster doesn't exist.

**BDR** a fork of Postgres that provides multimaster.

Application controlled replication makes app very complex.


### Summary
For HA use streaming replication, mix sync and async replication,
consider patroni or repmgr.
Don't use logical replication for HA!

Read query offloading is for query replication.

Data distribution is for logical one.

QA:
- How much latency is added for sync replication?  
Depends on hardware and network.

- Can you create separate indexes on read replicas?  
With binary replication NO.

- Pgpool for replication?  
Don't use it. May be used for balancing.


## PostgreSQL and Kubernetes

Talk by Zalando.

>300 databases in datacenter. Hundreds of databases are transitioned to k8s.
Question: Why not use serverless Aurora?

Run standby server with replication delay. 9.6 version is used in production.

**pg\_mon** is used for monitoring. **pgview** to show data.

Grafana + KairosDB to store metrics.

StatefulSets from k8s allow to couple volume and pod.
2 pods: 1 master and 1 replica. If container0, then `pg_init` runs, else `pg_basebackup`.

Helm is easier to use. Abstracts some schdeuler complexity away from user.

Etcd is used for quorum of postgres cluster. Who should be promoted.
Patroni was intefrated in k8s.

**Spilo** packages Patroni and all dependencies.

**Postgres operator** is used to manage postgres in k8s.

OAUTH2PAM auth for postgres developed by them.

Operator dynamically changes EBS disk sizes with resize2fs.


## Elasticsearch Revolution

Components:
  - Cluster
  - Node
  - Index
  - Shard
  - Document
  - ID

Kibana Dev tools is a nice way to execute queries

Java garbage collector may corrupt data. WTF?

### What's new in ES?

ES 5.6 has upgrade assistant, bootstrap checks and more.

5 can be upgraded to 6 with rolling upgrade, no need to bring down entire cluster.

Floodstage to check for available resources. If floodstage watermark is reached
index is locked and you need to manually unlock it to allow writes.

Sequence numbers will be **always** incremented even if no changes applied.
Transaction track avoids excessive unneeded move of data in case of networking issues.
Cross datacenter replication. Types are going away. In 8.0 no more types.
In 6.0 there will be only 1 type. New reindex api to copy data to another index.

Adapting queue sizing that can guarantee operation time and SLA.
Pick best shard with exponentially weighted moving average.

Shards shrinking. You can predefine split factor for future splits.

**Rally** - a benchmarking tool.


# Sunday

## Documentation as source code

Author: Ferit Topcu

Why a style guide? - Because of variety

Don't repeat yourself DRY. Move fast. PRs without documentation are rejected.

Documetation should be easy to maintain, easy to customize.

Zalando uses Github Enterprise.

Markdown to write documentation.

Whenever PR is merged documentation is created immediately.

Tool called **stylelint** to create HTML styles for documentation. https://github.com/stylelint/stylelint

Library **documentationJS** for JavaScript documentation. http://documentation.js.org/

Available offline, static, prerendered, JS to do processing.
Entry level should be low. Jamstack is a great approach.

https://fabric-design.github.io/styleguide


## Provisioning vs Configuration Management Deployment vs Orchestration

Author: https://github.com/petems, guy from Puppetlabs

Infrastructure as code, **fog** a ruby tool to provision AWS infra.

**tugboat** a cli for DigitalOcean. https://github.com/petems/tugboat

Golden image considerations, vedor specific image formats.

**Deployer** a php type of capistrano.

Sometimes config management is a deployment tool.

**Bolt** a command executor for Puppet. You can define configuration as yaml and execute puppet modules with it.
Bolt is a more easier mcollective. Probably a replacement.

There's is place for config management in container world.

Building docker images with puppet is not a good idea in a long term.


## Kubernetes load balancing for virtual machines

**KubeVirt** to control VMs with k8s. oVirt is a base for it.

**Dscheduler**  reschedules containers to rebalance the cluster. Finds the pods that can be evicted.
Minimal disturbance on the cluster.

Load balancer to find better placement on nodes.


## MySQL InnoDB Cluster

Group replication for MySQL that allows udates anywhere - master-master,
virtually synchronous replication, automatic failover.

Router sits on top of cluster.

**MySQL Shell** is a new multi-language interface. Can be used to perform data queries.
Document and relational models support. **AdminAPI** is inteagrated in Shell and allows
cluster orchestration.

Mysql 8 has lots of changes, like restart command.


## Containerd

2016 v0.0.1 -> 2017 v1.0.0

Can be used beyond Docker. Runs on top of runc.

`ctr` command line interface for containerd.

**Snaphotter** part of containerd that manages snapshots.

Kubernetes has cri-containerd.

No requirements for docker engine to be for running.

Q: Can containerd build images?


## Kubernetes security practices

Clusterrolebinding ?

Restrict access to API servers with firewall

Use Network Policies.

seccomp, apparmor, selinux prevent break out of container.
`unshare` command to change namespaces.

Restrict kubelet permissions: RBAC, rotate kubelet certificates.

**istio** is a service mesh on top of k8s, proxies data between services,
end-to-end encryption, policy is managed by central server.


## Container security practices

`DOCKER_CONTENT_TRUST=1` to enable image verification

`docker trust` experimental command to verify image, set certificates, etc.

Seccomp restricst system calls based on policy.

docker bench security to audit security in containers

lynis is a tool to audit dockerfiles.

Use capabilities to controll

