# Collecting and Storing Metrics

Once we know what kind of [metrics](https://github.com/AICoE/data-driven-development/tree/main/examples/kebechet/metrics.md) we are interested in, we should be able to define them, collect them and store them.

Based on the components described in the [tooling](https://github.com/AICoE/data-driven-development/tree/main/examples/kebechet/tooling.md) section, there are different ways to store metrics.


## Prometheus Client

This [library](https://github.com/prometheus/client_python) helps to add instrumentation to the code and implements the following [Prometheus metric type](https://prometheus.io/docs/concepts/metric_types/):

- `Counter` - A counter is a cumulative metric that represents a single monotonically increasing counter whose value can only increase or be reset to zero on restart. For example, you can use a counter to represent the number of requests served, tasks completed, or errors.

- `Gauge` - A gauge is a metric that represents a single numerical value that can arbitrarily go up and down.

- `Histogram` - A histogram samples observations (usually things like request durations or response sizes) and counts them in configurable buckets. It also provides a sum of all observed values.

- `Summary` - Similar to a histogram, a summary samples observations (usually things like request durations and response sizes). While it also provides a total count of observations and a sum of all observed values, it calculates configurable quantiles over a sliding time window.

This Python Prometheus client [library](https://github.com/prometheus/client_python) lets you define and expose internal metrics via an HTTP endpoint on your application’s instance. When Prometheus scrapes your instance's HTTP endpoint, the client library sends the current state of all tracked metrics to the server.

### Three Step Demo

This demo shows how to interact with the Prometheus API client and is available from the official Python Prometheus client [library](https://github.com/prometheus/client_python).

**Step 1**: Install the client
```
pip install prometheus-client
```

**Step 2**: Paste the following into a Python file and run it
```python
from prometheus_client import start_http_server, Summary
import random
import time

# Create a metric to track time spent and requests made.
REQUEST_TIME = Summary('request_processing_seconds', 'Time spent processing request')

# Decorate function with metric.
@REQUEST_TIME.time()
def process_request(t):
    """A dummy function that takes some time."""
    time.sleep(t)

if __name__ == '__main__':
    # Start up the server to expose the metrics.
    start_http_server(8000)
    # Generate some requests.
    while True:
        process_request(random.random())
```

**Step 3**: Visit [http://localhost:8000/](http://localhost:8000/) to view the metrics

From one easy to use decorator you get:
  * `request_processing_seconds_count`: Number of times this function was called.
  * `request_processing_seconds_sum`: Total amount of time spent in this function.

Prometheus's `rate` function allows calculation of both requests per second,
and latency over time from this data.

In addition if you're on Linux the `process` metrics expose CPU, memory and
other information about the process for free!

Check the [library](https://github.com/prometheus/client_python) docs for more information.


## Flask Prometheus exporter

This [library](https://github.com/rycus86/prometheus_flask_exporter) provides HTTP request metrics to export into Prometheus. It can also track method invocations using convenient functions.

**Step 1**: Install the client
```
pip install prometheus-flask-exporter
```

**Step 2**: Paste the following into a Python file and run it

```python
from flask import Flask, request
from prometheus_flask_exporter import PrometheusMetrics

app = Flask(__name__)
metrics = PrometheusMetrics(app)

# static information as metric
metrics.info('app_info', 'Application info', version='1.0.3')

@app.route('/')
def main():
    pass  # requests tracked by default

@app.route('/skip')
@metrics.do_not_track()
def skip():
    pass  # default metrics are not collected

@app.route('/<item_type>')
@metrics.do_not_track()
@metrics.counter('invocation_by_type', 'Number of invocations by type',
         labels={'item_type': lambda: request.view_args['type']})
def by_type(item_type):
    pass  # only the counter is collected, not the default metrics

@app.route('/long-running')
@metrics.gauge('in_progress', 'Long running requests in progress')
def long_running():
    pass

@app.route('/status/<int:status>')
@metrics.do_not_track()
@metrics.summary('requests_by_status', 'Request latencies by status',
                 labels={'status': lambda r: r.status_code})
@metrics.histogram('requests_by_status_and_path', 'Request latencies by status and path',
                   labels={'status': lambda r: r.status_code, 'path': lambda: request.path})
def echo_status(status):
    return 'Status: %s' % status, status
```

Check the [library](https://github.com/rycus86/prometheus_flask_exporter) docs for more information.


## Dump report to S3


### boto3

You can use the Python AWS SDK [boto3](https://github.com/boto/boto3) to create, configure, and manage AWS services, such as Amazon Elastic Compute Cloud (Amazon EC2) and Amazon Simple Storage Service (Amazon S3). The SDK provides an object-oriented API as well as low-level access to AWS services.

**Step 1**: Install the library
```
pip install boto3
```

**Step 2**: Paste the following into a Python file with your env variables and run it

```python
import boto3

# Download files from S3
s3_endpoint_url = os.environ["OBJECT_STORAGE_ENDPOINT_URL"]
s3_access_key = os.environ["AWS_ACCESS_KEY_ID"]
s3_secret_key = os.environ["AWS_SECRET_ACCESS_KEY"]
s3_bucket = os.environ["OBJECT_STORAGE_BUCKET_NAME"]

# Create an S3 client
s3 = boto3.client(
    service_name="s3",
    aws_access_key_id=s3_access_key,
    aws_secret_access_key=s3_secret_key,
    endpoint_url=s3_endpoint_url,
)

```

This will start a client that can be used to perform different actions, e.g. `upload`.

```python
s3.upload_file(
    Bucket=s3_bucket, Key=key, Filename=filename
)
```


### thoth-storages

Kebechet uses boto3 library through [thoth-storages](https://github.com/thoth-station/storages), which is storage and database adapter for Project Thoth.


**Step 1**: Install the library
```
pip install thoth-storages
```

**Step 2**: Access Ceph and store data

To access data on Ceph, you need to know ``aws_access_key_id`` and ``aws_secret_access_key`` credentials
of the endpoint you are connecting to.

Absolute file path of the data you are acccessing is constructed as: ``s3://<bucket_name>/<prefix_name>/<file_path>``

You can either configure these environemnt variables to initilaize the data handler:

| Variable name | Content |
| ------------- | ------------------ |
| ``S3_ENDPOINT_URL`` | Ceph Host name |
| ``CEPH_BUCKET`` | Ceph Bucket name |
| ``CEPH_BUCKET_PREFIX`` | Ceph Prefix |
| ``CEPH_KEY_ID`` | Ceph Key ID |
| ``CEPH_SECRET_KEY`` | Ceph Secret Key |


```python
from thoth.storages.ceph import CephStore
s3 = CephStore()
```
Or you can initialize the object directly with them:

```python
from thoth.storages.ceph import CephStore
ceph = CephStore(
    key_id=<aws_access_key_id>,
    secret_key=<aws_secret_access_key>,
    prefix=<prefix_name>,
    host=<endpoint_url>,
    bucket=<bucket_name>)
```

After initialization, you are ready to store the data.

```python
s3.connect()

try:
    # For dictionary stored as json
    s3.store_file(<file_path>, <file_id>)

except Exception:
    # File could not be stored

```

## Argo Workflow Metrics

[Argo workflows metrics](https://argoproj.github.io/argo-workflows/metrics/) can push automatically to [Prometheus](https://prometheus.io/). [Argo](https://argoproj.github.io/argo-workflows/workflow-controller-configmap/) emits a certain number of controller metrics that inform on the state of the controller at any given time. Furthermore, users can also define their own custom metrics to inform on the state of their Workflows.

You can add a metrics section to your Argo workflow [template](https://github.com/thoth-station/thoth-application/blob/master/adviser/base/openshift-templates/adviser.yaml) like so:

```yaml
apiVersion: template.openshift.io/v1
kind: Template
metadata:
  name: adviser
  annotations:
    description: "Thoth: Adviser"
    openshift.io/display-name: "Thoth: Adviser"
    tags: thoth,ai-stacks,adviser
    template.openshift.io/documentation-url: https://github.com/Thoth-Station/
    template.openshift.io/long-description: >
      This template defines resources needed to run recommendation logic of Thoth to OpenShift.
    template.openshift.io/provider-display-name: "Red Hat, Inc."
  labels:
    app: thoth
    template: adviser
    component: adviser

parameters:
  - name: THOTH_ADVISER_JOB_ID
    required: true
    description: A unique dentifier of adviser job.
    displayName: Adviser id
  - ...

objects:
  - apiVersion: argoproj.io/v1alpha1
    kind: Workflow
    metadata:
      name: "${THOTH_ADVISER_JOB_ID}"
      labels:
        app: thoth
        component: adviser
    spec:
      serviceAccountName: argo
      activeDeadlineSeconds: 3000
      ttlStrategy:
        secondsAfterCompletion: 300
        secondsAfterSuccess: 300
        secondsAfterFailure: 300
      entrypoint: adviser

      metrics:
        prometheus:
          - name: status_counter
            help: "Count of workflow by status"
            labels:
              - key: name
                value: adviser
              - key: status
                value: "{{workflow.status}}"
            counter:
              value: "1"

          - name: duration_seconds_histogram
            help: "Duration of workflow when succeded"
            when: "{{workflow.status}} == Succeeded"
            labels:
              - key: name
                value: adviser
            histogram:
              buckets:
                - 5
                - 10
                - 30
                - 60
                - 120
                - 180
                - 300
                - 600
                - 900
              value: "{{workflow.duration}}"

```

You can also add a metrics section to a particular [task](https://github.com/thoth-station/thoth-application/blob/master/adviser/base/argo-workflows/advise.yaml) in your Argo Workflow:


```yaml
---
apiVersion: argoproj.io/v1alpha1
kind: WorkflowTemplate
metadata:
  name: advise
  annotations:
    operation: adviser
spec:
  templates:
    - name: advise
      metrics:
        prometheus:
          - name: task_status_counter
            help: "Count of workflow task by status"
            labels:
              - key: name
                value: adviser
              - key: status
                value: "{{status}}"
            counter:
              value: "1"

          - name: task_duration_seconds_histogram
            help: "Duration of workflow task when succeded"
            when: "{{status}} == Succeeded"
            labels:
              - key: name
                value: adviser
            histogram:
              buckets:
                - 5
                - 10
                - 30
                - 60
                - 120
                - 180
                - 300
                - 600
                - 900
              value: "{{duration}}"
```
