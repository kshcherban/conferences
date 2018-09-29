# All Systems Go Linux Conference

All Systems Go! is a conference focused on foundational user-space Linux
technologies. Its goal is to provide a gathering place for both contributors
and users of projects that make up the foundation of modern Linux systems.

![registration](registration.JPG)

## TOC

* [Friday](#friday-28-september)
    * [Functional resource control using cgroup2](#functional-resource-control-using-cgroup2)
    * [From physical to cloud to container One system to manage them all](#from-physical-to-cloud-to-container-one-system-to-manage-them-all)
    * [Playing with casync @Instagram](#playing-with-casync-@instagram)
    * [Fluent Bit: Solving Logging Challenges for Cloud Native Environments](#fluent-bit-solving-logging-challenges-for-cloud-native-environments)
    * [Cilium Bringing the BPF Revolution to Kubernetes Networking and Security](#cilium-bringing-the-bpf-revolution-to-kubernetes-networking-and-security)
    * [Container Run times and Fun times](#container-run-times-and-fun-times)
    * [Portable Services are Ready to Use](#portable-services-are-ready-to-use)
    * [Is my system fast?](#is-my-system-fast)
* [Saturday](#saturday-29-september)
    * [Container Runtimes draw some lines](#container-runtimes-draw-some-lines)
    * [Passive filesystem verification](#passive-filesystem-verification)
    * [Replacing Docker with Podman](#replacing-docker-with-podman)


# Friday 28 September

## Functional resource control using cgroup2

> Tejun Heo, Johannes Weiner @Facebook

![loft room](loft.JPG)

The idea to create transparent reource restriction for processes so that memory
leaks will not bring system down.

### Issues
* .high and .max aren't work conserving
* OOM killer doesn't protect workload health
* no good io controller to use
* `mmap_sem` and readahead

### Solutions
* memory.low and memory.min, liftu up, don't push down
* PSI resource pressure metric, system wide and per-cgroup if i had more of
  this resource i might have been able to run this percentage faster
* oomd - the gentler and more perceptive grim reaper helps kernet when resource
  isolation breaks down, workload QoS, context-aware decisions and actions
* io.latency - completion latency based IO control can be used both on hard
  disks and SSGs, works on `blk-mq`
* switch to btrfs to avoid ext4 journaling issues

### Setup
* switch to btrfs, multi-100k machines running it
* if there's no swap all anon memory is memcached
* enabled everywhere except for the main workload
* slice applications into:
    *  host critical (ssh, journald, rsyslog)
    * workload
    * system

### Results

Memory protections work much better than OOM killer

IO protections work slightly better on HDDs but much better on SSDs

Full OS resource isolation, proportion IO control for complex workloads

http://opensource.fb.com/#linux


## From physical to cloud to container One system to manage them all

> Klaus Kampf @SUSE

As a sys-admin i want to 
 * manage ny infra
 * provision systems
 * build the right artifacts

 Uyuni, Suse Manager is an answer to Red Hat Satellite Is an opnionated branch
 of Spacewalk
 * simple installation
 * salt configuration
 * container/kubernetes integration

Supports physical, PXE Boot (redfish planned), IPMI supported

Supports openstack, VMware, public cloud

https://github.com/uyuni-project/uyuni


## Playing with casync @Instagram

Why?  To deploy every commit from master to production.

CAPKG - package definition, manifest, store.

Allows to save lots of space, network traffic.
https://github.com/systemd/casync

Not using docker because of lots of deploys and overhead that it brings.
![casync at instagram](casync.JPG)


## Fluent Bit Solving Logging Challenges for Cloud Native Environments

> Eduardo Silva

Operational perspective
* discover logs

logging context matters in big container clusters; labels, annotations,
namespaces;

Written in C, low memory and CPU footprint, pluggable architecture, built-in
security with TLS.

Has input pligins, filter plugins.  Data is seralized with MessagePack.  Event
loop design, coroutines lus scripting, oauth support, special annotations for
k8s

https://github.com/fluent/fluent-bit


## Cilium Bringing the BPF Revolution to Kubernetes Networking and Security

> Thomas Graf

BPF gives an ability to extend a kernel/system call with a program.  eBPF -
extended BPF BPFd poverful linux tracing remote targets tool by google BPF/XDP
10x performance over IPVS, project from facebook available katran

Funny quote about iptables
>OH: "In any team you need a tank, a healer, a damage dealer, someone with
>crowd control abilities, and another who knows iptables"

![cilium](cilium.JPG)

cilium a local agent that generates a BPF program and attach it to pod.
Mode 1: encapsulation, Mode 2 direct routing

BPF-based - per-CPU hash table

Identity based security, embedding tokens in packets

https://cilium.io/


![bpf](bpf.JPG)


## Container Run times and Fun times

> Lindsay Salisbury @facebook

Using systemd inside a container

Tupperware, custom built container system

dbus > systemd replaces spawn/bash invocations

systemd-nspawn used for btrfs image building?

* contract for defining services
* interface for interacting with kernel abstraction
* oderly startup/shutdown
* flexible service composition

services defined as individual self-contained components, bind mounted into
container FS

Package with systemd units, metadata and service files. Kind of SDK for devs.
Kind of SDK for devs.

Secrets are managed with agent that controls applications.


## Portable Services are Ready to Use

> Lennart Poettering

Portable services: resource bundle + integration + sandboxing

Goal of portable services is to **leave no artifacts**.  Goal: fell like a
native service.  "Superprivilege containers"

Systemd now supports 3 formats: sysv, native, portable service.

Images for services, like `RootImage=`.

Image requires systemd unit files and `/var/lib/os-release`

Custom image format, dynamic users, d-bus usage.

`portablectl` new tool.  Profiles: default, strict, trusted, nonetwork.

Build tool: `mkosi`

![systemd](systemd.JPG)


## Is my system fast

> Frank Becker @Amazon

Computer systems are complex. Most software applications are distributed and
expected to scale. That does not make them any simpler. Further, there is the
real world all of that is expected to work in. To analyze performance of such a
system isn't less complex. Luckily, there is help. The Open Source universe is
full of excellent tools that can help. The talk introduces a few of them.

The talk walks down the path of comparing two Linux servers that look pretty
much the same from the specs. Introducing test-tools like stress-ng, sysjitter,
fio, packetdrill, and friends it is shown how to get a good overview of the
systems CPU, memory, disk, and network performance.

Since benchmark results are usually time variant a few basic statistical terms
are refreshed and used for visualizing those results employing different chart
types.

The field of performance analysis is wide. There isn't more time in 45 minutes
than just to scratch the surface. At the end of the talk the audience will have
a few pointers of how to start evaluating their own Linux systems to provide
the right performance for their applications.

CPU, Memory, Disk IO, Network

Median is more robust than mean and should be less favorable.

Beware to not measure cached latency.

Book to read: Systems Performance - Brendan Gregg

Heatmap chart types, flame graphs.

CPU benchmark, SpecCPU, stress-ng.

Disk metrics: latency, throughput, queue depth, iops. fio tool to measure disk
io.

Network performance:
* transmit/receive
* throughput
* latency

CPU jitter - Sloarflare sysjitter


# Saturday 29 September

## Container Runtimes draw some lines

> Vincent Batts

Future of connecting to the container runtime as docker phases out

Since docker came on the scene in 2013, for many it was the first they'd heard
of containers. They existed before, and an have exploded since then. Today if
you intend on running containers in production it means at the kubernetes
level, though what is happening below that? what does this mean for direct
access and on-going developer experience?

systemd-nspawn, lxc - history of containerization.

lmctfy - let me container that for you
https://github.com/google/lmctfy

rkt, Dec 2014, spec-first design (appC spec)

OCI spec appeared in 2015, image and runtime in 2017, distribution spec 2018

Different runtimes:
* cri-o started in 2016, mainly for k8s
* alibaba/pouch - yet another container runtime https://github.com/alibaba/pouch
* oracle/railcar - written in rust, https://github.com/oracle/railcar
* nvidia fork of runc
* google/gvisor

Link to presentation https://github.com/vbatts/talks/tree/master/2018/09/asg-container_runtimes


## Passive filesystem verification

> Vincent Batts

A more generic approach to ensure you have what you'd expect to have

A side effect of the many new ways to package filesystems (here's looking at
you, containers!), is that filesystems are being copied around without many of
the features that traditional packaging provided (i.e. `rpm -qV ...`). Much
progress has been made for reproducible digests of containers. In this talk
Vincent Batts will review options for distributing filesystems with
reproducibility, and verifying the at-rest outcomes.

Agenda:
* packaging
* content addressibility
* compression
* reproducible archives
* verify at rest

Before docker 1.8 layers ids were garbage.

Go used inconsistent compression algorithms, builds were not reproducible.
After go added some architecture improvements, all images were invalid.

Image verification:

* mtree port to linux
* go-mtree
* libarchive-formats
* casync-mtree
* umoci unpack (https://github.com/openSUSE/umoci/blob/master/cmd/umoci/unpack.go)

Link to presentation https://github.com/vbatts/talks/tree/master/2018/09/asg-passive_fs_validation


## The State of Your Supply Chain

> Andrew Martin

Container security often focuses on runtime best-practices whilst neglecting
delivery of the software in the supply chain. Application, library, and OS
vulnerabilities are a likely route to data exfiltration, and emerging
technologies in the container ecosystem offer a new opportunity to mitigate
this risk.

Treating containers as immutable artefacts and injecting configuration allows
us to "upgrade" images by rebuilding and shipping whole software bundles,
avoiding configuration drift and state inconsistencies. This makes it possible
to constantly patch software, and to easily enforce governance of artefacts
both pre- and post-deployment.

In this talk we detail an ideal, security-hardened container supply chain,
describe the current state of the ecosystem, and dig into specific tools.
Grafeas, Kritis, in-toto, Clair, Micro Scanner, TUF, and Notary are covered,
and we demo how to gate container image pipelines and deployments on
cryptographically verified supply chain metadata.

Problems:
* vulnerabilities in dependencies
* deliberate backdoors
* compromised downloads

hermetic builds, isolated build env
reproducible builds, only helps security if you actually do reproduce it

runtime configuration - adherence to PodSecurityPolicy and Kubesec risk based
on runtime configuration of the images that comprise a pod.

Controlled base images, static analysis of code.

Tools to solve problems.

Update framework (TUF), notary project.

Clair, auqa microscanner, anchore - image scanning projects.

**Grafeas** project to audit & govern the software supply chain. https://grafeas.io

**in-toto** a framework to provide whole supply chain security.


## TAPS API for networks

> Theresa Enghardt

Is a draft for paths, networks selections. Provides API for applications?
to select better or specified network connection, i.e. with lowest latency and
not cellular.

Apple Network.framework.


## Desktop Linux Platform Issues

> Simon Peter

Developers need dependable platform. All successful OS distributions are platforms.

Linux distributions break userspace.

Systemd to manage desktop applications, database of apps, MIME types, handle
multiple versions, etc.

https://gitlab.com/probono/platformissues


## Replacing Docker with Podman

> Dan Walsh

This talk will describe all of the reasons for podman, all of its features
demonstrate its functionality,

I will cover the background of podman, how we built it, why we built it,
I will demonstrate using it in multiple different ways,
Running containers
building container images
Communicating with it via var link, cockpit integration.
Communicating with it from a remote machine.

Hates docker for unknown reasons.

Docker deamon became fat, containers are childs of this deamon.
podman replaces docker cli.

Can be executed without root. Nice.

`buildah` is used to build images with podman. Also doesn't require root.

When using user namespaces it chowns filesystem of the image on the fly :(

Podman has proper systemd integration. Can run systemd inside pod.

Has API, python bindings, cockpit support.

Don't support:
* containers autostart
* swarm
* notary
* healthchecks
* docker api
* volumes

https://github.com/containers/libpod

