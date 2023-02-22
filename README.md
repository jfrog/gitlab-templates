# JFrog GitLab Templates

# Table of Contents

- [Overview](#overview)

# Overview
This repository holds example templates for GitLab integration with the JFrog Platform, for multiple common build tools.
The templates are based on the [Setup JFrog](scripts/README.md#Setup-JFrog-for-GitLab-CI) script that can be simply included and referenced in a gitlab-ci.yml pipeline.

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