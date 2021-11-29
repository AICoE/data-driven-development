# Monitoring Metrics

Based on the type of application, we can define relevant metrics that help developers, product owners, users etc gain more insights regarding the performance, operation, usage of the application. Metrics can be categorized based on the different personas we would like to target such as:

- `Product Owner` - Project's key stakeholder who communicate the vision of the application to the team/stakeholders and ensure the feasibility of the application with respect to business objectives
- `Project Manager` - Supervisors of other teams who would like to adopt the application for their use-cases and oversee the successful delivery of the application
- `Technical Architect` -  Provides technical feasibility of requirements, advise on technology choices and manage the technical roadmap
- `Operations` - Primarily focused on application management, application maintenance and instrumental in automation of application development processes
- `Analyst` - Work with both developers and users to assess the product, utilizing data and user feedback to suggest improvements. Responsible for analyzing metrics to continually improve the application
- `Users` - Users interacting with the application (who we are building the product for) and this can probably have sub categories for the different type of users

# Recommended Metrics: Kebechet


| <strong>Metric</strong> | <strong>Metric Definition</strong> | <strong>Metric Calculation</strong> | <strong>Targeted Persona</strong> | <strong>Data Source</strong> |
| ------------- | ------------------ | ------------------ | ------------------ | ------------------ |
| Number of active users | Number of repos where Kebechet is active | sum(KebechetGithubAppInstallations Table slug with is_active=True) | Product Owner | PostgreSQL database (exposed by Thoth metrics-exporter) |
| Number of new active users (daily/weekly/monthly) | Number of new repos where Kebechet is active |   | Product Owner |   |
| Number of stacks maintained | Number of software stacks maintained by Kebechet | sum(KebechetGithubAppInstallations Table slug with is_active=True, distinct=True) | Product Owner | Thoth database (exposed by metrics-exporter) |
| Number of users per manager (thoth adviser) | Total number of users per manager (thoth adviser) | sum(KebechetGithubAppInstallations Table slug with is_active=True, distinct=True, for KebechetGithubAppInstallations.thoth_advise_manager) | Product Owner | Thoth database (exposed by metrics-exporter) |
| Number of PR opened by Kebechet per manager (thoth adviser) | Total number of PR opened by Kebechet |  | Product Owner | Github (collected by mi) |
| Number of PR merged by Kebechet per manager (thoth adviser) | Total number of PR opened by Kebechet merged |  | Product Owner | Github (collected by mi) |
| Successful Kebechet service delivered percentage (thoth adviser) |  | (Number of PR merged by Kebechet ÷ Number of PR opened by Kebechet) * 100 | Product Owner | Ceph (processed by mi) |
| Average time to merge Kebechet PRs (thoth adviser) | Time taken to merge a PR opened by Kebechet |  | Product Owner | Ceph (processed by mi) |
| Number of PR closed/rejected by Kebechet per manager (thoth adviser) | Total number of PR opened by Kebechet closed/rejected |  | Product Owner | Github (collected by mi) |
| Failed Kebechet service delivered percentage (thoth adviser) |  | (Number of PR closed/rejected  by Kebechet ÷ Number of PR opened by Kebechet) * 100 | Product Owner | Ceph (processed by mi) |
| Number of PR opened by Kebechet per manager (thoth adviser) per type of PR | Total number of PR opened by Kebechet for specific reasons (e.g. CVE update, new package release) |  | Product Owner | Github (collected by mi) |
| Number of PR merged by Kebechet per manager (thoth adviser) per type of PR | Total number of PR merged by Kebechet for specific reasons (e.g. CVE update, new package release) |  | Product Owner | Github (collected by mi) |
| Average time to merge Kebechet PRs (thoth adviser) per type of PR | Time taken to merge a PR opened by Kebechet |  | Product Owner | Ceph (processed by mi) |
| Number of PR closed/rejected by Kebechet per manager (thoth adviser) per type of PR | Total number of PR closed/rejected by Kebechet closed/rejected for specific reasons (e.g. CVE update, new package release)  |  | Product Owner | Github (collected by mi) |
| Time taken to review PRs |  |  | Analyst | Ceph (processed by mi) |
| Average time taken by Kebechet workflow |  |  | Operations | Argo Workflow Controller |
| Average time taken by Adviser workflow task |  |  | Operations | Argo Workflow Controller |
| Average time taken by Kebechet run results workflow task (in adviser) |  |  | Operations | Argo Workflow Controller |
| Percentage of adviser requests scheduled respected to requested |  |  | Operations | Ceph (processed by advise-reporter) |
| Percentage of adviser requests from Kebechet succeeded |  | number of successful adviser per source_type=KEBECHET | Operations | Ceph (processed by advise-reporter) |
| Percentage of adviser requests from Kebechet failed |  | number of failed adviser per source_type=KEBECHET | Operations | Ceph (processed by advise-reporter) |
| Number of Kafka messages sent per internal trigger to schedule Kebechet administrator |  |  | Operations | Kafka producers (e.g. package-release) |
| Number of Kebechet workflows scheduled by workflow-controller |  |  | Operations | Thoth investigator (Kafka consumer) |
| Database up |  |  | Operations | exposed by Thoth metrics-exporter |
| Kafka up |  |  | Operations | exposed by Thoth metrics-exporter |
| Ceph up |  |  | Operations | exposed by Thoth metrics-exporter |
| User-API up |  |  | Operations | collected from Openshift Monitoring |
| Thoth Investigator up |  |  | Operations | collected from Openshift Monitoring |
| Thoth workflow controller (backend-namespace) |  |  | Operations | collected from Openshift Monitoring |
| Thoth workflow controller (middletier-namespace) |  |  | Operations | collected from Openshift Monitoring |
