# Tooling

From the architecture schema of [Kebechet](https://github.com/operate-first/data-driven-development/tree/main/examples/kebechet/README.md), you can see that Kebechet requires multiple components and workflows to provide services. The major components and their functions are described below.

## Technical Infrastructure

This section describes the categories of components involved in the operational life of Kebechet application:

- `service` -> services that are used by the application and provided by the platform

- `application` -> components or libraries that are created for the application

- `monitoring` -> components or libraries that are created/used for monitoring and pushing metrics

- `analytics` -> components or libraries that are created/used for providing analysis on the data collected


### Service components

* [Kafka](https://kafka.apache.org/) - an open-source distributed event streaming platform
* [Argo](https://argoproj.github.io/) - family of open source tools for Kubernetes to run workflows, manage clusters, and do GitOps right
* [Ceph](https://ceph.io/en/) - an open-source, distributed storage system

### Application components

* [PostgreSQL Database](https://www.postgresql.org/) - open source object-relational database
* [Thoth storages](https://github.com/thoth-station/storages) - Thoth storages and database adapter
* [Thoth User API](https://github.com/thoth-station/user-api) - Thoth service interface and Kafka producer
* [Thoth Investigator](https://github.com/thoth-station/investigator) - Kafka consumer of messages sent by different components
* [Argo workflow controller](https://argoproj.github.io/argo-workflows/workflow-controller-configmap/) - Controller for scheduling and handling Argo workflows
* [Argo workflows](https://argoproj.github.io/argo-workflows/). Each of them contain several steps:

    - [Kebechet Administrator Argo workflow](https://github.com/thoth-station/thoth-application/blob/master/kebechet/base/openshift-templates/kebechet-administrator.yaml) - run Kebechet administrator that decides based on triggers which repo needs to receive a Kebechet update
    - [Adviser Argo workflow](https://github.com/thoth-station/thoth-application/blob/master/adviser/base/openshift-templates/adviser.yaml) - run Thoth adviser recommender system
    - [Kebechet Argo workflow](https://github.com/thoth-station/thoth-application/blob/master/kebechet/base/openshift-templates/kebechet.yaml) - run Kebechet component logic
    - [Solver Argo workflow](https://github.com/thoth-station/thoth-application/blob/master/solver/base/openshift-templates/solve_and_sync-template.yaml) - run solver component to analyze python packages for different runtime environments, stores knowledge in the graph database and sends messages for scheduling Security workflows


### Monitoring Components

* [Prometheus](https://prometheus.io/) - Monitoring tool for collecting time series metrics. To learn more about Prometheus, refer to this documentation [here](https://github.com/AICoE/data-driven-development/blob/main/data-sources/prometheus.md)
* [Grafana](https://grafana.com/) - Visualization tool for creating dashboards/graphs of the metrics
* [Thoth MI Scheduler](https://github.com/thoth-station/mi-scheduler) - Tool to collect GitHub data
* [GitHub Alertmanager](https://github.com/prometheus/alertmanager) - Alertmanager that handles alerts and creates GitHub issues in the chosen repository to notify the respective teams
* [Thoth Metrics Exporter](https://github.com/thoth-station/metrics-exporter) - Component that evaluates content metrics from the database and sends metrics to Prometheus
* [Thoth Graph metrics exporter](https://github.com/thoth-station/graph-metrics-exporter) - Component that evaluates content metrics for long running processes (evaluating bloat data for example) from the database and sends metrics to Prometheus
* [PostgreSQL metrics exporter](https://github.com/prometheus-community/postgres_exporter) - Components that collect operational metrics from the database and send them to Prometheus


### Analytical Components

* [Prometheus API client](https://github.com/AICoE/prometheus-api-client-python) - A Python wrapper for the Prometheus HTTP API
* [Thoth SLO-reporter](https://github.com/thoth-station/slo-reporter) - Aggregate, analyze and combine metrics from different sources (Prometheus or Ceph data), providing a report on SLI by email
* [Thoth reporter](https://github.com/thoth-station/reporter) - Aggregate, analyze and produce metrics from adviser reports that are stored on Ceph for visualization on Superset
* [Thoth MI Scheduler](https://github.com/thoth-station/mi-scheduler) - component that schedules mi workflows based on list of maintained GitHub repositories:
    - [MI Argo workflow](https://github.com/thoth-station/thoth-application/blob/master/mi-scheduler/base/openshift-templates/mi-run-template.yaml) - component that collect GitHub metrics from GitHub repositories
* [Superset](https://superset.apache.org/) - Superset is a modern data exploration and visualization platform
