# JFrog GitLab Templates

# Table of Contents

- [Overview](#overview)

# Overview
This repository holds example templates for GitLab integration with the JFrog Platform, for multiple common build tools.

The templates are based on the Setup JFrog script that can be easily included and referenced in your GitLab pipeline.

The Setup JFrog script downloads, installs and configures JFrog CLI, so that it can be used as part of your GitLab pipeline.

In addition, the Setup includes the following features:
* There's no need to add the build name and build number options and arguments to commands which accept them. All build related operations will be automatically recorded with the following default build name and build number.

    * Build name: `$CI_PROJECT_PATH_SLUG-$CI_COMMIT_REF_NAME`

    * Build number: `$CI_PIPELINE_ID`


* The script overrides the GitLab Container Registry (if used) with the configured Artifactory Docker Registry.

## Installation
1. Set the connection details to the JFrog Platform as GitLab CI/CD Variables.
   [Need a FREE JFrog environment in the cloud?](#Set-Up-a-FREE-JFrog-Environment-in-the-Cloud)
2. [Optional] Create a Docker repository in Artifactory, and add its URL as a GitLab CI/CD Variable.
3. Include the Setup JFrog script in your GitLab pipeline:
```yaml
include:
  - remote: 'https://releases.jfrog.io/artifactory/jfrog-cli/gitlab/.setup-jfrog.yml'
  # OR download it and include it from your project.
  - local: '.setup-jfrog.yml'
  # OR download it and include it from a common project.
  - project: 'my-group/my-project'
    file: '/script/.setup-jfrog.yml'
```

Then, reference the setup script from any `script` or `before_script` sections in your GitLab pipeline:
```yaml
job:
  script:
    - !reference [.setup_jfrog, script]
    # OR for windows:
    - !reference [.setup_jfrog_windows, script]
```

### Storing JFrog Platform Servers
You can set the connection details to your JFrog Platform by using one of the following variables combinations:

1. JF_URL (no authentication)
2. JF_URL + JF_USER + JF_PASSWORD (basic authentication)
3. JF_URL + JF_ACCESS_TOKEN (authentication using a JFrog Access Token. NOTE: If using the container registry, a username is also required when using this option)

### Additional Optional Variables
Configurations can be done via Project Settings > CI/CD > Variables:

| Variable                | Usage                                                                                                                                                                                                            |
|-------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| JF_DOCKER_REGISTRY      | Docker registry in Artifactory. For more info, see [Getting Started with Artifactory as a Docker Registry](https://www.jfrog.com/confluence/display/JFROG/Getting+Started+with+Artifactory+as+a+Docker+Registry) |
| JFROG_CLI_BUILD_PROJECT | JFrog project key to be used by commands which expect build name and build number. Determines the project of the published build.                                                                                |
| JFROG_CLI_VERSION       | Use a different CLI version than the one configured in the pipe. The minimal version is: 2.17.0                                                                                                                  |

List of other optional environment variables can be found in the CLI official [documentation](https://www.jfrog.com/confluence/display/CLI/CLI+for+JFrog+Artifactory#CLIforJFrogArtifactory-EnvironmentVariables).

### Limitations
1. If the `JF_DOCKER_REGISTRY` and `JF_ACCESS_TOKEN` variables are set, then the `JF_USER` variable is required.
2. Build info collection is unavailable when:
    * Working against the Docker Registry without JFrog CLI.
    * Running separate jobs on temporary agents or docker containers.

### Behind the scenes
The actual setup scripts are maintained under the [jfrog-cli repository](https://github.com/jfrog/jfrog-cli/blob/v2/build/gitlab/.setup-jfrog.yml). 
It includes two hidden jobs with scripts, which can be referenced after the file is included.

## Set Up a FREE JFrog Environment in the Cloud
Need a FREE JFrog environment in the cloud to use with these templates? Just run one of the following commands in your terminal. The commands will do the following:

1. Install JFrog CLI on your machine.
2. Create a FREE JFrog environment in the cloud for you.

**MacOS and Linux using cUrl**
```
curl -fL "https://getcli.jfrog.io?setup" | sh
```

**Windows using PowerShell**
```
powershell "Start-Process -Wait -Verb RunAs powershell '-NoProfile iwr https://releases.jfrog.io/artifactory/jfrog-cli/v2-jf/[RELEASE]/jfrog-cli-windows-amd64/jf.exe -OutFile $env:SYSTEMROOT\system32\jf.exe'" ; jf setup
```