# CodeBuild

### How it works <a href="#key_concepts" id="key_concepts"></a>

AWS CodeBuild is a fully managed build service that compiles your source code, runs unit tests, and produces artifacts that are ready to deploy.

![CodeBuild in action ](<../../../.gitbook/assets/image (120).png>)

1. You create a build project (our next lab)&#x20;
2. CodeBuild uses the project to create a build environment
3. CodeBuild downloads the source (from S3, CodeCommit or elsewhere) and uses a file called Buildpsec.yml to run build commands
4. Any possible output (artifact) is stored in an S3 bucket.&#x20;

### Key Concepts <a href="#key_concepts" id="key_concepts"></a>

**Build Process** Process that converts source code files into an executable software artifact. It may include the following steps: compiling source code, running tests, and packaging software for deployment.

**Continuous Integration** Software development practice of regularly pushing changes to a hosted repository, after which automated builds and tests are run.

**Build Environment** Represents a combination of the operating system, programming language runtime, and tools that CodeBuild uses to run a build.

**Buildspec** Collection of build commands and related settings, in YAML format, that CodeBuild uses to run a build.

**Build Project** Includes information about how to run a build, including where to get the source code, which build environment to use, which build commands to run, and where to store the build output

## Buildspec.yml

A _buildspec_ (build specification) is a collection of build commands and related settings, in YAML format, that CodeBuild uses to run a build.&#x20;

The syntax is as follows:

```yaml
version: 0.2

run-as: Linux-user-name

env:
  shell: shell-tag
  variables:
    key: "value"

proxy:
  upload-artifacts: no | yes
  logs: no | yes

batch:

        
phases:
  install:
    run-as: Linux-user-name
    on-failure: ABORT | CONTINUE
    runtime-versions:
      runtime: version
    commands:
      - command
    finally:
      - command
      
  pre_build:
  
  build:

  post_build:

reports:

artifacts:

cache:


```

The sections are:

* version
* run-as
* env&#x20;
* proxy&#x20;
* phases&#x20;
* reports
* artifacts
* cache

### Example buildspec.yml

```yaml
version: 0.2 # most recent version 

env:
  variables:
    JAVA_HOME: "/usr/lib/jvm/java-8-openjdk-amd64"
  parameter-store:  # password is stored in AWS Parameter Store (coming on day 5) 
    LOGIN_PASSWORD: /CodeBuild/dockerLoginPassword

# what CodeBuild should actually do as it builds 
phases:
  install:
    commands:
      - echo Entered the install phase...
      - apt-get update -y
      - apt-get install -y maven  
    finally:
      - echo This always runs even if the update or install command fails 
  pre_build:
    commands:
      - echo Entered the pre_build phase...
      - docker login -u User -p $LOGIN_PASSWORD
    finally:
      - echo This always runs even if the login command fails 
  build:
    commands:
      - echo Entered the build phase...
      - echo Build started on `date`
      - mvn install
    finally:
      - echo This always runs even if the install command fails
  post_build:
    commands:
      - echo Entered the post_build phase...
      - echo Build completed on `date`

reports:
  arn:aws:codebuild:your-region:your-aws-account-id:report-group/report-group-name-1:
    files:
      - "**/*"
    base-directory: 'target/tests/reports'
    discard-paths: no
  reportGroupCucumberJson:
    files:
      - 'cucumber/target/cucumber-tests.xml'
    discard-paths: yes
    file-format: CUCUMBERJSON # default is JUNITXML
artifacts:
  files:
    - target/messageUtil-1.0.jar
  discard-paths: yes
  secondary-artifacts:
    artifact1:
      files:
        - target/artifact-1.0.jar
      discard-paths: yes
    artifact2:
      files:
        - target/artifact-2.0.jar
      discard-paths: yes
cache:
  paths:
    - '/root/.m2/**/*'
```
