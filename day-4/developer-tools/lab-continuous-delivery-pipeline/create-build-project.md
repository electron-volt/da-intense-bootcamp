# Create build project

We are going to use CodeBuild to build the source code previously stored in your GitHub repository. We will

* Create a build project with AWS CodeBuild
* Set up GitHub as the source provider for a build project
* Run a build on AWS CodeBuild.

## Create a project in CodeBuild

1. In a new browser tab open the AWS CodeBuild Console.
2. Click the **orange "Create project" button**.
3. In the "Project name" pick a name for your project. Mine is pipeline-example.
4. Select **"GitHub"** from the "Source provider" dropdown menu.&#x20;
5. Visually confirm that the **"Connect using OAuth" radio button** is selected.
6. Click the **white "Connect to GitHub" button**. After clicking this button, a new browser tab will open asking you to give AWS CodeBuild access to your GitHub repo.
7. Click the **green "Authorize aws-codesuite" button**.
8. Type in your GitHub password.
9. Click the **orange "Confirm" button**.
10. Select "Repository in my GitHub account."
11. Type **"aws-elastic-beanstalk-express-js-sample"** in the search field.

![choose repository](<../../../.gitbook/assets/image (285).png>)

13\. Visually confirm that **"Managed Image"** is selected.

14\. Select **"Amazon Linux 2"** from the "Operating system" dropdown menu.

15\. Select **"Standard"** from the "Runtime(s)" dropdown menu.

16\. Select **"aws/codebuild/amazonlinux2-x86\_64-standard:3.0"** from the "Image" dropdown menu.

17\. Visually confirm that **"Always use the latest image for this runtime version"** is selected for "Image version."

18\. Visually confirm that **"Linux"** is selected for "Environment type."

19\. Visually confirm that **"New service role"** is selected.

### Buildspec&#x20;

1. Select **"Insert build commands."**
2. Click on **"Switch to editor."**
3. **Replace** the Buildspec in the editor with the code below:

```yaml
version: 0.2
phases:
    build:
        commands:
            - npm i --save
artifacts:
    files:
        - '**/*'
```

Complete the creation with **Create build project**.

## Test the build

Try building the project. We expect this level of success:

![](<../../../.gitbook/assets/image (239).png>)

## Where we're at

![](<../../../.gitbook/assets/image (111).png>)
