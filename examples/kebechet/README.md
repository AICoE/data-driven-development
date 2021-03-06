# Kebechet

> I'm Kebechet, goddess of freshness. I will keep your sources and dependencies fresh and up-to-date.

[Kebechet](https://github.com/thoth-station/kebechet) is a SourceOps bot that automates updating dependencies of your project. Currently, it supports managing and
updating Python projects based on [pipenv](https://docs.pipenv.org/) files (`Pipfile` and `Pipfile.lock`) or
`requirements.txt`/`requirements.in` files (see [pip-tools](https://pypi.org/project/pip-tools/) - Kebechet is a
replacement for it).

## Architecture

The components and workflows of Kebechet can be found in the architecture schema below:

![Kebechet Schema](../../assets/images/kebechet-schema.png)

## Sequence Diagrams

The two images below show two sequence diagrams of the behaviour of Kebechet in two cases:

- when some user interacts with the GitHub App (external-trigger).

![Kebechet Schema](../../assets/images/kebechet-schema-external-trigger.png)

- when internal components triggers learning of new knowledge and share of this knowledge with users (internal trigger)

![Kebechet Schema](../../assets/images/kebechet-schema-internal-trigger-package-release.png)

As you can see, there are different workflows that are triggered to manage your Python dependencies using Thoth's recommendation system. You can request directly for guidance to the service (external trigger) or your repository will be automatically updated with the optimal Python software stack for your project without you having to lift your finger (internal trigger). You can have a detailed description of all the components involved into Kebechet [here](./tooling.md).


## Monitoring

- [Tooling](https://github.com/AICoE/data-driven-development/blob/main/examples/kebechet/tooling.md) -> This section describes all components involved and used for monitoring and analytics.

- [Metrics](https://github.com/AICoE/data-driven-development/blob/main/examples/kebechet/metrics.md) -> This section describes all the metrics defined for monitoring Kebechet

- [Collecting & Storing metrics](https://github.com/AICoE/data-driven-development/blob/main/examples/kebechet/collecting_and_storing_metrics.md) -> This section describes how metrics are collected by the different component and how they are stored.

- [Accessing metrics](https://github.com/AICoE/data-driven-development/blob/main/examples/kebechet/accessing_metrics.md) -> This section describes how you can access the stored metrics.

- [Visualizing metrics](https://github.com/AICoE/data-driven-development/blob/main/examples/kebechet/visualizing.md) -> This section describes how you can visualize the metrics directly or post processing them.

- [Alerting](https://github.com/AICoE/data-driven-development/blob/main/examples/kebechet/alerting.md) -> This section describes how you can create alerts.
