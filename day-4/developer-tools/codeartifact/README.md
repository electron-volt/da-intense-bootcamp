# CodeArtifact

### Concepts

#### Domain&#x20;

Repositories are aggregated into a higher-level entity known as a _domain_. All package assets and metadata are stored in the domain, but they are consumed through repositories. The domain allows organizational policy to be applied across multiple repositories, such as which accounts can access repositories in the domain, and which public repositories can be used as sources of packages.

#### Repository

A CodeArtifact _repository_ contains a set of package versions, each of which maps to a set of assets. Repositories are polyglot—a single repository can contain packages of any supported type.

#### Package

A package is a bundle of software and the metadata that is required to resolve dependencies and install the software. **** A _package version_ identifies the specific version of a package, such as `@types/node 12.6.9`.&#x20;

#### Upstream repository&#x20;

One repository is _upstream_ of another when the package versions in it can be accessed from the repository endpoint of the downstream repository, effectively merging the contents of the two repositories from the point of view of a client.

#### Asset

An _asset_ is an individual file stored in CodeArtifact that is associated with a package version, such as an npm `.tgz` file or Maven POM and JAR files.

#### Package namespace

Some package formats support hierarchical package names to organize packages into logical groups and help avoid name collisions.

Example: For Maven packages, the package namespace corresponds to the Maven groupID. The Maven package `org.apache.logging.log4j:log4j` has a groupID (package namespace) of `org.apache.logging.log4j` and the artifactID (package name) `log4j`.&#x20;

![Name collisions aren't the only hazards he avoided](<../../../.gitbook/assets/image (13).png>)
