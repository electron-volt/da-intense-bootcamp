# CloudWatch

Amazon CloudWatch monitors your Amazon Web Services (AWS) resources and the applications you run on AWS in real time.

* You can create custom dashboards to display metrics about your custom applications
* You can create alarms that watch metrics and send notifications or automatically make changes to the resources you are monitoring when a threshold is breached.&#x20;

With CloudWatch, you gain system-wide visibility into resource utilization, application performance, and operational health.

We've seen some CloudWatch logs when we invoked our Lambda functions.&#x20;

## CloudWatch concepts

### Namespace

A _namespace_ is a container for CloudWatch metrics. Metrics in different namespaces are isolated from each other, so that metrics from different applications are not mistakenly aggregated into the same statistics.

The AWS namespaces typically use the following naming convention: `AWS/service`. For example, Amazon EC2 uses the `AWS/EC2` namespace.&#x20;

### Metrics

_Metrics_ are the fundamental concept in CloudWatch. A metric represents a time-ordered set of data points that are published to CloudWatch. Think of a metric as a variable to monitor, and the data points as representing the values of that variable over time.

Metrics are uniquely defined by a name, a namespace, and zero or more _dimensions_. Each data point in a metric has a time stamp, and (optionally) a unit of measure.

For example, the CPU usage of a particular EC2 instance is one metric provided by Amazon EC2.&#x20;

### Dimensions

A _dimension_ is a name/value pair that is part of the identity of a metric. You can assign up to 10 dimensions to a metric.&#x20;

AWS services that send data to CloudWatch attach dimensions to each metric. You can use dimensions to filter the results that CloudWatch returns. For example, you can get statistics for a specific EC2 instance by specifying the `InstanceId` dimension when you search for metrics.

### Alarms

You can use an _alarm_ to automatically initiate actions on your behalf. An alarm watches a single metric over a specified time period, and performs one or more specified actions, based on the value of the metric relative to a threshold over time.&#x20;

Full list of all concepts: [https://docs.aws.amazon.com/AmazonCloudWatch/latest/monitoring/cloudwatch\_concepts.html](https://docs.aws.amazon.com/AmazonCloudWatch/latest/monitoring/cloudwatch\_concepts.html)

