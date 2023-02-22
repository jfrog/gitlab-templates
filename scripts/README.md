# Setup JFrog for GitLab CI

The Setup JFrog script downloads, installs and configures JFrog CLI, so that it can be used as part of your GitLab pipeline.

In addition, the Setup includes the following features:

* There's no need to add the build name and build number options and arguments to commands which accept them. All build related operations will be automatically recorded with the following default build name and build number.

    * Build name: `$CI_PROJECT_PATH_SLUG-$CI_COMMIT_REF_NAME`

    * Build number: `$CI_PIPELINE_ID`
* The script overrides the GitLab Container Registry (if used) with the configured Artifactory Docker Registry.

## Installation
1. Set the connection details to the JFrog Platform as GitLab CI/CD Variables.
2. [Optional] Create a Docker repository in Artifactory, and add its URL as a GitLab CI/CD Variable.
3. Include the Setup JFrog script in your GitLab pipeline:
```yaml
include:
  - remote: 'https://releases.jfrog.io/artifactory/setup-jfrog-gitlab/[RELEASE]/setup-jfrog.yml'
  # OR download it and include it from your project.
  - local: 'setup-jfrog.yml'
  # OR download it and include it from a common project.
  - project: 'my-group/my-project'
    file: '/script/.gitlab-ci-template.yml'
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

## Limitations
1. If the `JF_DOCKER_REGISTRY` and `JF_ACCESS_TOKEN` variables are set, then the `JF_USER` variable is required.
2. Build info collection is unavailable when:
   * Working against the Docker Registry without JFrog CLI.
   * Running separate jobs on temporary agents or docker containers.