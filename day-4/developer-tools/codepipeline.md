# CodePipeline

There is a LOT of theory about CodePipeline. The way I - a simpleton - conceptualise it is like this:

![Pipeline consists of stages.](<../../.gitbook/assets/image (261).png>)

## Pipeline <a href="#concepts-pipelines" id="concepts-pipelines"></a>

A _pipeline_ is a workflow construct that describes how software changes go through a release process. Each pipeline is made up of a series of _stages_.

### **Stages**

A stage might be a build stage, where the source code is built and tests are run. Each stage contains actions that are performed on the application artifacts. Your source code is an example of an artifact.&#x20;

### **Actions**

An _action_ is a set of operations performed on application code and configured so that the actions run in the pipeline at a specified point.&#x20;

Valid CodePipeline action types are `source`, `build`, `test`, `deploy`, `approval`, and `invoke`.&#x20;

### Pipeline executions <a href="#concepts-executions" id="concepts-executions"></a>

An _execution_ is a set of changes released by a pipeline. Each pipeline execution is unique and has its own ID. An execution corresponds to a set of changes.

Pipeline executions traverse pipeline stages in order. Valid statuses for pipelines are `InProgress`, `Stopping`, `Stopped`, `Succeeded`, `Superseded`, and `Failed`.

### Transitions <a href="#concepts-transitions" id="concepts-transitions"></a>

A _transition_ is the point where a pipeline execution moves to the next stage in the pipeline.

