# Moselwal Digitalagentur Build Tools

<details>
<summary>Table of Content</summary>

[TOC]

</details>

## Overview
Welcome to the Moselwal Digitalagentur Build Tools repository. This project provides a set of GitLab CI/CD components designed to streamline and automate the build process for PHP-based projects. The build tools are designed to be flexible, with configurable inputs to tailor the CI/CD pipeline to your specific needs.

## License
This program is free software: you can redistribute it and/or modify it under the terms of the GNU General Public License as published by the Free Software Foundation, either version 3 of the License, or (at your option) any later version.

This program is distributed in the hope that it will be useful, but WITHOUT ANY WARRANTY; without even the implied warranty of MERCHANTABILITY or FITNESS FOR A PARTICULAR PURPOSE. See the GNU General Public License for more details.

You should have received a copy of the GNU General Public License along with this program. If not, see <https://www.gnu.org/licenses/>.

## Components

### Component: build:extension:backend
This job is responsible for setting up the backend for extension during the build stage.

#### Inputs

| **Input**                   | **Description**                                                                                     | **Example**                         | **Default Value**                                                     |
|-----------------------------|-----------------------------------------------------------------------------------------------------|-------------------------------------|-----------------------------------------------------------------------|
| `stage`                     | (Optional) The stage in the CI/CD pipeline where this job will run.                                 | `test`                              | `build`                                                               |
| `rules-config`              | (Optional) Configuration rules that determine when the job should run.                             | See below for default rules         | See below for default rules                                           |
| `image-config`              | (Optional) The Docker image to use for the job. This should use semantic versioning.               | `registry.example.com/php/7.4/ci:latest` | `registry.moselwal.io/devops/images/php/$PHP_VERSION/ci:latest`        |
| `tags-config`               | (Optional) Tags used to select specific runners to execute the job.                                | `docker`, `linux`                   | `- docker`                                                            |
| `additional-script-begin`   | (Optional) Add additional script at the beginning of the script.                                   | `echo "Start"`                      | `''`                                                                  |
| `additional-script-end`     | (Optional) Add additional script at the end of the script.                                         | `echo "End"`                        | `''`                                                                  |

###### Default `rules-config`
```yaml
- if: $CI_COMMIT_TAG
  when: never
- if: $CI_COMMIT_REF_NAME == $CI_DEFAULT_BRANCH
- if: $CI_COMMIT_REF_NAME != 'release'
- if: $CI_COMMIT_REF_NAME == 'release'
  when: never
```

- **Stage**: `$[[ inputs.stage ]]`
- **Needs**: `["build:prepare"]`
- **Rules**: `$[[ inputs.rules-config ]]`
- **Image**: `$[[ inputs.image-config | expand_vars ]]`
- **Tags**: `$[[ inputs.tags-config ]]`

#### Artifacts
- **Untracked**: `true`
- **When**: `always`
- **Expire In**: `60 minutes`


### Component: build:extension:frontend
This job is responsible for setting up the frontend for extension during the build stage.

#### Inputs

| **Input**                   | **Description**                                                                                     | **Example**                         | **Default Value**                                                      |
|-----------------------------|-----------------------------------------------------------------------------------------------------|-------------------------------------|------------------------------------------------------------------------|
| `stage`                     | (Optional) The stage in the CI/CD pipeline where this job will run.                                 | `test`                              | `build`                                                                |
| `rules-config`              | (Optional) Configuration rules that determine when the job should run.                             | See below for default rules         | See below for default rules                                            |
| `image-config`              | (Optional) The Docker image to use for the job. This should use semantic versioning.               | `registry.example.com/php/7.4/ci:latest` | `${CI_DEPENDENCY_PROXY_GROUP_IMAGE_PREFIX}/node:$NODE_VERSION-alpine3.19` |
| `tags-config`               | (Optional) Tags used to select specific runners to execute the job.                                | `docker`, `linux`                   | `- docker`                                                             |
| `additional-script-begin`   | (Optional) Add additional script at the beginning of the script.                                   | `echo "Start"`                      | `''`                                                                   |
| `additional-script-end`     | (Optional) Add additional script at the end of the script.                                         | `echo "End"`                        | `''`                                                                   |

##### Default `rules-config`
```yaml
- if: $CI_COMMIT_TAG
  when: never
- if: $CI_COMMIT_REF_NAME == $CI_DEFAULT_BRANCH
- if: $CI_COMMIT_REF_NAME != 'release'
- if: $CI_COMMIT_REF_NAME == 'release'
  when: never
```

- **Stage**: `$[[ inputs.stage ]]`
- **Needs**: `["build:prepare"]`
- **Rules**: `$[[ inputs.rules-config ]]`
- **Image**: `$[[ inputs.image-config | expand_vars ]]`
- **Tags**: `$[[ inputs.tags-config ]]`
- **Allow Failure**: `true`

#### Artifacts 
  - **Untracked**: `true`
  - **Exclude**: `./Build/node_modules/*`
  - **When**: `always`
  - **Expire In**: `60 minutes`

### build:prepare
This job is responsible for preparing the environment and gathering necessary information for the build process during the build stage.

#### Inputs

| **Input**                   | **Description**                                                                                     | **Example**                         | **Default Value**                                                      |
|-----------------------------|-----------------------------------------------------------------------------------------------------|-------------------------------------|------------------------------------------------------------------------|
| `stage`                     | (Optional) The stage in the CI/CD pipeline where this job will run.                                 | `test`                              | `build`                                                                |
| `rules-config`              | (Optional) Configuration rules that determine when the job should run.                             | See below for default rules         | See below for default rules                                            |
| `image-config`              | (Optional) The Docker image to use for the job. This should use semantic versioning.               | `registry.example.com/bash:5-alpine3.19` | `${CI_DEPENDENCY_PROXY_GROUP_IMAGE_PREFIX}/bash:5-alpine3.19`         |
| `tags-config`               | (Optional) Tags used to select specific runners to execute the job.                                | `docker`, `linux`                   | `- docker`                                                             |
| `additional-script-begin`   | (Optional) Add additional script at the beginning of the script.                                   | `echo "Start"`                      | `''`                                                                   |
| `additional-script-end`     | (Optional) Add additional script at the end of the script.                                         | `echo "End"`                        | `''`                                                                   |

##### Default `rules-config`
```yaml
- if: $CI_COMMIT_TAG
- if: $CI_COMMIT_REF_NAME == $CI_DEFAULT_BRANCH
- if: $CI_COMMIT_REF_NAME != 'release'
- if: $CI_COMMIT_REF_NAME == 'release'
```

- **Stage**: `$[[ inputs.stage ]]`
- **Rules**: `$[[ inputs.rules-config ]]`
- **Image**: `$[[ inputs.image-config | expand_vars ]]`
- **Tags**: `$[[ inputs.tags-config ]]`

####  Artifacts
- **Reports**:
  - **Dotenv**: `build.env`

### Component: build:site:test:backend
This job is responsible for setting up the backend for testing during the build stage.

#### Inputs

| **Input**       | **Description**                                                                     | **Example**                              | **Default Value**                                                                                                                                              |
|-----------------|-------------------------------------------------------------------------------------|------------------------------------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------|
| `stage`         | (Optional) The stage in the CI/CD pipeline where this job will run.                 | `test`                                   | `build`                                                                                                                                                        |
| `rules-config`  | (Optional) Configuration rules that determine when the job should run.              | See below for default rules              | `- if: $CI_DEPLOY_FREEZE == null && $CI_COMMIT_TAG != null && $CI_PIPELINE_SOURCE != "schedule"`<br>`- if: $CI_PIPELINE_SOURCE == "schedule"`<br>`when: never` |
| `image-config`  | (Optional) The Docker image to use for the job. This should use semantic versioning. | `registry.example.com/php/7.4/ci:latest` | `registry.moselwal.io/devops/images/php/$PHP_VERSION/ci:latest`                                                                                                |
| `tags-config`   | (Optional) Tags used to select specific runners to execute the job.                 | `docker`, `linux`                        | `- docker`                                                                                                                                                     |
| `additional-script-begin` | (Optional) Add additional Script at the beginning of the Script.           | `composer update`                        | ''                                                                                                                                                             |
| `additional-script-end`| (Optional) Add additional Script at the end of the Script.                    | `rm -rf .composer`                       | ''                                                                                                                                                             |

##### Default `rules-config`
```yaml
- if: $CI_DEPLOY_FREEZE == null && $CI_COMMIT_TAG != null && $CI_PIPELINE_SOURCE != "schedule"
- if: $CI_PIPELINE_SOURCE == "schedule"
  when: never
```

- **Stage**: `$[[ inputs.stage ]]`
- **Needs**: `["build:prepare"]`
- **Rules**: `$[[ inputs.rules-config ]]`
- **Image**: `$[[ inputs.image-config | expand_vars ]]`
- **Tags**: `$[[ inputs.tags-config ]]`

#### Variables
- `GIT_SUBMODULE_STRATEGY`: `recursive`
- `GIT_SUBMODULE_DEPTH`: `1`

#### Artifacts
- **Untracked**: `true`
- **Paths**:
    - `./*`
- **Exclude**:
    - `./*.ssh-agent`
- **When**: `always`
- **Expire In**: `30 minutes`

## Contributing
Please read about CI/CD components and best practices at: https://docs.gitlab.com/ee/ci/components    
We welcome contributions to improve the Moselwal Digitalagentur Build Tools. Please read our [Contributions Doucmentation](CONTRIBUTING.md)

## Support
For support, please contact [Moselwal Digitalagentur GmbH](mailto:support@moselwal.de).