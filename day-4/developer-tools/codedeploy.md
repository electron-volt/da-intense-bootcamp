# CodeDeploy

CodeDeploy can be used to centrally manage and automate deployments to&#x20;

* EC2 instances or on-premises servers
* Lambda functions&#x20;
* ECS.

We are going to use CodeDeploy in the last lab of the day.&#x20;

## Deployments

### Deployment configuration &#x20;

During a deployment, traffic can be routed in the following ways:

* Canary - shifted in two increments.&#x20;
* Linear - shifted in equal increments, each the same size.&#x20;
* All-at-once - all traffic is shifted in one go.&#x20;

### Deployment type

For EC2 instances, deployments can be done in two ways:

* in-place: revisions are made to the existing instances (this is the only option for on-premises servers).&#x20;
* blue/green: a replacement environment is provisioned and updated first, then traffic is routed to the new environment. The original environment is either terminated or kept running for other uses.&#x20;

For ECS and Lambda, the only deployment type is blue/green.&#x20;

## AppSpec.yml

Like we have seen with CodeBuild, CodeDeploy also has an all-important YAML (or JSON) file. For CodeDeploy it is called **appspec.yml**.&#x20;

### AppSpec for ECS&#x20;

Appspec.yaml for ECS has three sections:

* version: currently 0.0
* resources: information about the ECS application to deploy
* hooks: lifecycle hooks.

```yaml
version: 0.0
resources: 
  ecs-service-specifications
Hooks:
  - BeforeInstall: "BeforeInstallHookFunctionName"
  - AfterInstall: "AfterInstallHookFunctionName"
  - AfterAllowTestTraffic: "AfterAllowTestTrafficHookFunctionName"
  - BeforeAllowTraffic: "BeforeAllowTrafficHookFunctionName"
  - AfterAllowTraffic: "AfterAllowTrafficHookFunctionName"
```

#### Lifecycle hooks for ECS

The hooks section for ECS deployments has these hooks. The deployment type will be blue/green for ECS.&#x20;

* `BeforeInstall` – Use to run tasks before the replacement task set is created.
* `AfterInstall`&#x20;
* `AfterAllowTestTraffic`&#x20;
* `BeforeAllowTraffic`&#x20;
* `AfterAllowTraffic`&#x20;

![](<../../.gitbook/assets/image (168).png>)

### AppSpec for Lambda

AppSpec.yaml for Lambda has three sections:

* version
* resources
* hooks

```yaml
version: 0.0
resources: 
  lambda-function-specifications
hooks:
   - BeforeAllowTraffic: BeforeAllowTrafficHookFunctionName
   - AfterAllowTraffic: AfterAllowTrafficHookFunctionName
```

#### Lifecycle hooks for Lambda

A lot simpler here, traffic either goes to Lambda or it doesn't:

* `BeforeAllowTraffic`&#x20;
* `AfterAllowTraffic`&#x20;

![](<../../.gitbook/assets/image (65).png>)

### AppSpec for EC2

AppSpec.yml for EC2 has five sections:

* version
* os: linux or windows&#x20;
* files: files to be copied to the instance
* permissions: what permissions to apply to files
* hooks

```yaml
version: 0.0
os: operating-system-name
files:
  source-destination-files-mappings
permissions:
  permissions-specifications
hooks:
  deployment-lifecycle-event-mappings
```

#### Lifecycle hooks for EC2

These depend on whether your servers on on-prem or in AWS. It also depends on the deployment type: in-place or blue/green. The hooks can include the following:

* `ApplicationStop`
* `DownloadBundle`&#x20;
* `BeforeInstall`
* `Install`&#x20;
* `AfterInstall`&#x20;
* `ApplicationStart` – if you used `ApplicationStop`.
* `ValidateService`
* `BeforeBlockTraffic`&#x20;
* `BlockTraffic`&#x20;
* `AfterBlockTraffic`&#x20;
* `BeforeAllowTraffic`
* `AllowTraffic`&#x20;
* `AfterAllowTraffic`

Example for an in-place deployment to a Linux server:

```yaml
version: 0.0
os: linux
files:
  - source: Config/config.txt
    destination: /webapps/Config
  - source: source
    destination: /webapps/myApp
hooks:
  BeforeInstall:
    - location: Scripts/UnzipResourceBundle.sh
    - location: Scripts/UnzipDataBundle.sh
  AfterInstall:
    - location: Scripts/RunResourceTests.sh
      timeout: 180
  ApplicationStart:
    - location: Scripts/RunFunctionalTests.sh
      timeout: 3600
  ValidateService:
    - location: Scripts/MonitorService.sh
      timeout: 3600
      runas: codedeployuser

```
