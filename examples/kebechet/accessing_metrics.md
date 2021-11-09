# Accessing Metrics

Once we have the metrics collected and stored, to better explore and analyze them, we should be able to programmatically access the metrics via relevant tools such as Jupyter notebooks.

Since a majority of our metrics are from [Prometheus](https://prometheus.io/), we find it best to explore the metrics using the [Prometheus API client](https://github.com/AICoE/prometheus-api-client-python) within [Jupyter](https://jupyter.org/) notebooks. Prometheus collects metrics (time series data) from configured targets at given intervals, evaluates rule expressions, displays the results, and can trigger alerts if some condition is observed to be true. The raw time series data obtained from a Prometheus host can sometimes be hard to interpret. To help better understand these metrics we have created a Python wrapper for the Prometheus http api for easier metrics processing and analysis.

### Connecting and Collecting Metrics from a Prometheus host

The `PrometheusConnect` module of the library can be used to connect to a Prometheus host. This module is essentially a class created for the collection of metrics from a Prometheus host. It stores the following connection parameters:

-   **url** - (str) url for the prometheus host
-   **headers** – (dict) A dictionary of http headers to be used to communicate with the host. Example: {“Authorization”: “bearer my_oauth_token_to_the_host”}
-   **disable_ssl** – (bool) If set to True, will disable ssl certificate verification for the http requests made to the prometheus host

```python
from prometheus_api_client import PrometheusConnect
prom = PrometheusConnect(url ="<prometheus-host>", disable_ssl=True)

# Get the list of all the metrics that the Prometheus host scrapes
prom.all_metrics()
```

You can also fetch the time series data for a specific metric using custom queries as follows:

```python
prom = PrometheusConnect()
my_label_config = {'cluster': 'my_cluster_id', 'label_2': 'label_2_value'}
prom.get_current_metric_value(metric_name='up', label_config=my_label_config)

# Here, we are fetching the values of a particular metric name
prom.custom_query(query="prometheus_http_requests_total")

# Now, lets try to fetch the `sum` of the metrics
prom.custom_query(query="sum(prometheus_http_requests_total)")
```

You can also use custom queries for fetching the metric data in a specific time interval. For example, let's try to fetch the past 2 days of data for a particular metric in chunks of 1 day:

```python
# Import the required datetime functions
from prometheus_api_client.utils import parse_datetime
from datetime import timedelta

start_time = parse_datetime("2d")
end_time = parse_datetime("now")
chunk_size = timedelta(days=1)

metric_data = prom.get_metric_range_data(
    "up{cluster='my_cluster_id'}",  # this is the metric name and label config
    start_time=start_time,
    end_time=end_time,
    chunk_size=chunk_size,
)
```

For more functions included in the `PrometheusConnect` module, refer to this [documentation.](https://prometheus-api-client-python.readthedocs.io/en/master/source/prometheus_api_client.html#module-prometheus_api_client.prometheus_connect)

### Getting Metrics Data as pandas DataFrames

To perform data analysis and manipulation, it is often helpful to have the data represented using a [pandas DataFrame](https://pandas.pydata.org/docs/user_guide/dsintro.html#dataframe). There are two modules in this library that can be used to process the raw metrics fetched into a DataFrame.

The `MetricSnapshotDataFrame` module converts "current metric value" data to a DataFrame representation, and the `MetricRangeDataFrame` converts "metric range values" data to a DataFrame representation. Example usage of these classes can be seen below:

```python
import datetime as dt
from prometheus_api_client import PrometheusConnect,  MetricSnapshotDataFrame, MetricRangeDataFrame

prom = PrometheusConnect()
my_label_config = {'cluster': 'my_cluster_id', 'label_2': 'label_2_value'}

# metric current values
metric_data = prom.get_current_metric_value(
    metric_name='up',
    label_config=my_label_config,
)
metric_df = MetricSnapshotDataFrame(metric_data)
metric_df.head()
""" Output:
+-------------------------+-----------------+------------+-------+
| __name__ | cluster      | label_2         | timestamp  | value |
+==========+==============+=================+============+=======+
| up       | cluster_id_0 | label_2_value_2 | 1577836800 | 0     |
+-------------------------+-----------------+------------+-------+
| up       | cluster_id_1 | label_2_value_3 | 1577836800 | 1     |
+-------------------------+-----------------+------------+-------+
"""

# metric values for a range of timestamps
metric_data = prom.get_metric_range_data(
    metric_name='up',
    label_config=my_label_config,
    start_time=(dt.datetime.now() - dt.timedelta(minutes=30)),
    end_time=dt.datetime.now(),
)
metric_df = MetricRangeDataFrame(metric_data)
metric_df.head()
""" Output:
+------------+------------+-----------------+--------------------+-------+
|            |  __name__  | cluster         | label_2            | value |
+-------------------------+-----------------+--------------------+-------+
| timestamp  |            |                 |                    |       |
+============+============+=================+====================+=======+
| 1577836800 | up         | cluster_id_0    | label_2_value_2    | 0     |
+-------------------------+-----------------+--------------------+-------+
| 1577836801 | up         | cluster_id_1    | label_2_value_3    | 1     |
+-------------------------+-----------------+------------=-------+-------+
"""
```

For more functions included in the `prometheus-api-client` library, please refer to this [documentation.](https://prometheus-api-client-python.readthedocs.io/en/master/source/prometheus_api_client.html)
