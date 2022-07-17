# Availability


Availability in the common practice is understood as a metric rather than a property of the system. While in the theory of distributed systems (especially, CAP theorem[^kleppmann]) it is more about property leading to confusion.

We will follow practical considerations and under availability will understand the following concepts.


> *A service level indicator (SLI) is a carefully defined quantitative measure of some aspect of the level of service that is being provided. It is a metric, not a target.*[^gcp]

For example, it can be a ratio of successfully handled requests.

> *A service level objective (SLO) specifies a target level for the reliability of your service. The SLO is a target value for an SLI.*[^gcp]

Service Level Agreement (SLA) declares your commitment to satisfy SLO.
SLO also determines your *error budget*, i.e. duration of the [allowed downtime](https://uptime.is/) within different time periods

Going distributed is just one of the ways to strengthen availability via adding scalabity and redundancy.

Clever design (including API), code quality assurance, proper algorithms, performance tuning, application monitoring, best DevSecOps practices, business and tech metrics collection significantly improves availability and should be considered first before introducing complexity of a distribution.

[^kleppmann]: Martin Kleppmann [A Critique of the CAP Theorem](https://arxiv.org/abs/1509.05393)

[^gcp]: Google Cloud Architecture Framework [Reliability principles](https://cloud.google.com/architecture/framework/reliability/principles)