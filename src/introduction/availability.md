# Availability


Availability in the common practice is understood as a metric rather than a property of the system. While in the theory of distributed systems (especially, CAP theorem[^kleppmann]) it is more about property leading to confusion.

We will follow practical considerations and under availability will understand the following concepts.


> *A service level indicator (SLI) is a carefully defined quantitative measure of some aspect of the level of service that is being provided. It is a metric, not a target.*[^gcp]

For example, it can be a ratio of successfully handled requests.

> *A service level objective (SLO) specifies a target level for the reliability of your service. The SLO is a target value for an SLI.*[^gcp]

Service Level Agreement (SLA) declares your commitment to satisfy SLO.
SLO also determines your *error budget*, i.e. duration of the [allowed downtime](https://uptime.is/) within different time periods

Going distributed is just one of the ways to strengthen availability via adding scalabity and redundancy.

While network being a primary source of failures for distributed systems, we should also take into consideration other resources related to a single node:
* Storage (disks are corrupted, drivers contain bugs, various filesystems have different memory orderings models and are mounted with different policies[^files-are-hard])
* CPU (processes are stopped while VM is migrated, system clocks may drift etc, cpu usage patterns, align with branch prediction and caches sizes)
* Memory (can be corrupted, defragmentated, what are the memory usage patterns)

So we should define *fault models* for different resources in our distributed system[^tigerbeetle], i.e. a specification of how our application handles different failures.

Clever design (including API), code quality assurance (including configuration changes tests[^configuration]), proper algorithms, performance tuning, application monitoring, best DevSecOps practices, business and tech metrics collection significantly improves availability and should be considered first before introducing complexity of a distribution.

[^kleppmann]: Martin Kleppmann [A Critique of the CAP Theorem](https://arxiv.org/abs/1509.05393)

[^gcp]: Google Cloud Architecture Framework [Reliability principles](https://cloud.google.com/architecture/framework/reliability/principles)

[^files-are-hard]: An eyes opening reading for the author was [Files are fraught with peril](https://danluu.com/deconstruct-files/) by Dan Luu - how difficult and error-prone is to use File API and how weak in terms of error-handling many filesystems are. General advice is to rely on already robust and battle-tested products like SQLite (which handles different edge cases) if you need some local persistent storage.

[^tigerbeetle]: See for an example the [Design Document](https://github.com/tigerbeetledb/tigerbeetle/blob/main/docs/DESIGN.md#fault-models) of TigerBeetle.

[^configuration]: see Dan Luu's [Reading postmortems](https://danluu.com/postmortem-lessons/)