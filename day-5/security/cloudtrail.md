# CloudTrail

AWS CloudTrail enables auditing, security monitoring, and operational troubleshooting. CloudTrail records user activity and API usage across AWS services as Events. CloudTrail Events help you answer the questions of "who did what, where, and when?"

CloudTrail records two types of events:&#x20;

* Management events capturing **control plane actions** on resources such as creating or deleting Amazon Simple Storage Service (Amazon S3) buckets, and&#x20;
* data events capturing **data plane actions** within a resource, such as reading or writing an Amazon S3 object.

CloudTrail uses these events in three features:

* **Trails** enables delivery and storage of events in Amazon S3, with optional delivery to Amazon CloudWatch Logs and EventBridge.
* **Insights** analyzes control plane events for anomalous behavior in API call volumes.
* **Event history** provides a 90-day history of control plane actions _for free_.&#x20;

## Event history

90 day event history is recorded by CloudTrail by default for free for all users.&#x20;

Navigate to CloudTrail and click Event history. You will see a list of all API calls made in your account for the past 90 days.&#x20;

![My history after the SQS labs. ](<../../.gitbook/assets/image (199).png>)
