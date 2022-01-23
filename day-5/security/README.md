# Security

Last but not least, let's talk about security.&#x20;

## KMS

Key Management Service is a managed service that makes it easy for you to create and control the cryptographic keys that are used to protect your data.

## Systems Manager Parameter Store

AWS Systems Manager provides a centralized store to manage your configuration data, whether plain-text data such as database strings or secrets such as passwords. This allows you to separate your secrets and configuration data from your code. Parameters can be tagged and organized into hierarchies, helping you manage parameters more easily. For example, you can use the same parameter name, "db-string", with a different hierarchical path, "dev/db-string” or “prod/db-string", to store different values.

Note: you cannot rotate secrets in Parameter store. For that you will need to use Secrets manager.

## Secrets Manager

A secrets management service that helps you protect access to your applications, services, and IT resources. This service enables you to easily **rotate**, manage, and retrieve database credentials, API keys, and other secrets throughout their lifecycle.

From AWS FAQ:

**"Q: What is the difference between Secrets Manager and Parameter Store?**\
[AWS Secrets Manage](https://aws.amazon.com/secrets-manager/)r is a service to manage the lifecycle for the secrets used in your organization centrally including rotation, audit, and access control. Secrets Manager helps you meet your security and compliance requirements by enabling you to rotate secrets automatically. Secrets Manager offers built-in integration for MySQL, PostgreSQL, and Amazon Aurora on Amazon RDS that's extensible to other types of secrets by customizing Lambda functions.

AWS Systems Manager Parameter Store provides secure, hierarchical storage for configuration data management, which can include secrets. Data such as database connection strings, passwords, and license codes can be stored as parameter values and can be audited and access controlled. Values stored can be either plain text or encrypted data. You can then reference values by using the unique name of the parameter. You can reference Systems Manager parameters to build generic configuration and automation scripts for use across AWS services such as Amazon ECS and AWS CloudFormation."

Similar sounding services for sure. We are going to do a lab using Parameter Store, mainly because it's free.&#x20;

## CloudTrail

CloudTrail is audit-logging. It answers the question: who did what and when?&#x20;

## Quick one-liners

We will quickly mention some of the technologies that can pop up in the exam. Typically all you need to know is what the technology is and what it's used for.

### CloudHSM&#x20;

Single tenant HSM or hardware security module in the cloud. FIPS 140-2 level 3 (KMS is only level 2).&#x20;

### Inspector

Automated vulnerability management.&#x20;

### Macie

Looks for PII (personally identifiable information) in S3 buckets.&#x20;

### WAF

Web application firewall. Useful for blocking malicious IP's and finding layer 7 web attacks like XSS, SQLi and other OWASP top 10 favourites. Can be used with CloudFront, ALB and so on.&#x20;

### Shield

Ddos protection. Shield standard protection is always on, you can pay extra for Shield advanced.&#x20;

### AWS Artifact

Not to be confused with the developer tool. AWS Artifact is where you can download reports and contracts, for example to show that AWS are HIPAA or PCI-DSS compliant in some region.&#x20;

### Other security services

Full list of products from AWS' website [https://aws.amazon.com/products/security/](https://aws.amazon.com/products/security/)

