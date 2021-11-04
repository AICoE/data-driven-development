# Alerting

Alerting on metrics is essential to monitoring. They allow you to spot problems anywhere in your infrastructure, application, etc so that you can rapidly identify their causes and minimize service degradation and disruption. An alert should communicate something specific about your systems such as “90% of all web requests are taking more than 0.5s to process and respond." Receiving these alerts allows you to respond quickly to issues and provide better service, and it also saves time by freeing you from continual manual inspection of metrics.

You can get notified of these alerts through emails, Slack etc. Our team uses the [GitHub Alertmanager](https://github.com/m-lab/alertmanager-github-receiver) to handle all the alerts. The GitHub Alertmanager is a webhook receiver that creates GitHub issues from alerts. To learn more about setting up the GitHub Alertmanager, you can refer to our [documentation](https://www.operate-first.cloud/operations/sre/incident-management/github-receiver-setup.md).

# Alerts: Kebechet

You can find below the alerts defined for Kebechet.


| <strong>Alert</strong> | <strong>Alert Definition</strong> | <strong>Alert Rule</strong> | <strong>Mitigation Plan</strong> | <strong>Alert Provider</strong> |
| ------------- | ------------------ | ------------------ | ------------------ | ------------------ |
| Thoth User-API is down | Alert for User API down | up{instance="{CLUSTER_INSTANCE}"} < 1 | Verify status of the cluster. | Prometheus GitHub AlertManager |
| service requests missed producing results | Alert for mismatch between number of requests and documents produced | thoth_reporter_requests_gauge{env="{CLUSTER_INSTANCE}", component="{THOTH_SERVICE}"} - thoth_reporter_reports_gauge{env="{CLUSTER_INSTANCE}", component="{THOTH_SERVICE}"} | Verify status of Thoth investigator, Kafka, User_API. | Prometheus GitHub AlertManager |
| purge job issues opened missed | Alert for mismatch between number of requests and documents produced | thoth_number_purge_issues_total{env="zero-prod"} - thoth_number_purge_issues_created{env="zero-prod"} > 0 | Retrigger Purge Job. | Github Issue by Prometheus GitHub AlertManager |
| thoth middletier number of worflows is 0  | Alert for 0 workflows running in Thoth Middletier namespace | argo_workflows_count{field="workflow-controller-metrics-thoth-middletier-prod.apps.smaug.na.operate-first.cloud:80", status="Running"} < 1 | Verify status of Data Ingestion in Thoth or add more workload. | Github Issue by Prometheus GitHub AlertManager |
| mismatch between analyzed solvers and known solvers  | Alert for number of solvers from Solver ConfigMap and from Thoth database not matching. | thoth_graphdb_solvers_number_match{field="metrics-exporter-thoth-infra-prod.apps.smaug.na.operate-first.cloud:80"} == 1 | Check solvers in Solver ConfigMap and in Thoth database. | Github Issue by Prometheus GitHub AlertManager |
| Issue connecting to Kafka |Alert for issue in connection with Kafka | thoth_kafka_connection_issues{field="metrics-exporter-thoth-infra-prod.apps.smaug.na.operate-first.cloud:80"} == 1 | Verify status of Kafka. | Github Issue by Prometheus GitHub AlertManager |
| Kafka message is halted |Alert for halted messages | thoth_investigator_halted_topics{field="investigator-faust-web-thoth-infra-prod.apps.smaug.na.operate-first.cloud:80"} == 1 | Activate message again using endpoint in Thoth investigator. | Github Issue by Prometheus GitHub AlertManager |
| Issue connecting to Thoth database |Alert for issue in connection with Thoth database | thoth_graphdb_connection_issues{field="metrics-exporter-thoth-infra-prod.apps.smaug.na.operate-first.cloud:80"} == 1 | Verify status of Thoth database. | Github Issue by Prometheus GitHub AlertManager |
| thoth-storages version mismatch | alembic version mismatch between components and database | thoth_graph_db_component_revision_check{env="{CLUSTER_INSTANCE}"} == 1 | Release all impacted components with thoth-storages in the correct version. | Github Issue by Prometheus GitHub AlertManager |
| Thoth database is corrupted | Database schema is corrupted, all services need to be stopped | thoth_graphdb_is_corrupted{field="metrics-exporter-thoth-infra-prod.apps.smaug.na.operate-first.cloud:80"} == 1 | Analyze Thoth database. | Github Issue by Prometheus GitHub AlertManager |
| Thoth database has multiple alembic versions | Alert for alembic version table corruption | thoth_graphdb_alembic_table_check{field="metrics-exporter-thoth-infra-prod.apps.smaug.na.operate-first.cloud:80"} == 1 | Analyze Thoth database. | Github Issue by Prometheus GitHub AlertManager |

The Kebechet rules triggered by Prometheus can be found [here](https://github.com/thoth-station/thoth-application/blob/master/monitoring/overlays/moc-prod/alerting-rules.yaml).
