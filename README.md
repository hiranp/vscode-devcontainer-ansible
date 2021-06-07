# Introduction

Dockerfile to provide an isolated Ansible environment for interactive use in the command-line, Visual Studio Code's devcontainer feature, or in a CI/CD pipeline.

Image based off of [ansible toolset](https://github.com/ansible-community/toolset), which bundles Ansible Community
Toolset bundle for developing and testing tools in a single container.

Added support for [Visual Studio Code Remote - Containers](https://code.visualstudio.com/docs/remote/containers) extension, so you can develop directly inside the docker image.

Finally, direct support for AWS and GCloud cli.

## What is bundled inside the container

Generally the containers should bundle the latest stable versions of the tools below. An exact list can be seen in [requirements.txt](https://github.com/ansible-community/toolset/blob/main/requirements.txt) file used for building the container.

* [ansible](https://pypi.org/project/ansible/)
* [ansible-lint](https://pypi.org/project/ansible-lint/)
* [molecule](https://pypi.org/project/molecule/) and most of its plugins
* [pytest](https://pypi.org/project/pytest/) and several of its plugins
* [yamllint](https://yamllint.readthedocs.io/en/stable/) which is used by
  ansible-lint itself.

## VSCode Support

[VScode Devcontainer](https://github.com/microsoft/vscode-dev-containers)

```console
   "extensions": [
        "ms-python.python",
        "tht13.python",
        "zbr.vscode-ansible",
        "vscoss.vscode-ansible",
        "redhat.vscode-yaml",
        "oderwat.indent-rainbow",
        "yzhang.markdown-all-in-one",
        "donjayamanne.githistory",
        "eamodio.gitlens",
        "waderyan.gitblame"
    ]
```

## Ansible Collection

The following Ansible collections are installed. You can edit `requirement.yml` to change it.

* [amazon.aws](https://galaxy.ansible.com/amazon/aws)
* [google.cloud](https://galaxy.ansible.com/google/cloud)

[![Docker Pulls](https://img.shields.io/docker/pulls/hiranp/ansible-toolset)](https://hub.docker.com/repository/docker/hiranp/ansible-toolset) [![GitHub Actions](https://img.shields.io/github/ansibleflow/status/hiranp/ansible--toolset/Push%20to%20Docker)](https://github.com/hiranp/ansible-toolset/actions)

### Installation

Automated builds are available on [Docker Hub](https://hub.docker.com/r/hiranp/ansible).

```bash
docker pull hiranp/ansible-toolset
```

But you can also build your own if you want.

```bash
docker build -t hiranp/ansible github.com/hiranp/ansible-toolset
```

```bash
#Local Build
$ docker build -t hiranp/ansible-toolset .devcontainer/
```

### Quick Start

#### Interactive

Running from the command-line as below allows for ad-hoc commands, and mounts the current directory for interactive usage and testing of playbooks

```bash
docker run -it --rm -v "${PWD}:/workspace" -w=/workspace --entrypoint=/bin/bash hiranp/ansible-toolset
```

Run playbook:

```bash
docker run -v "${PWD}":/workspace:ro -v ~/.ansible/roles:/root/.ansible/roles -v ~/.ssh:/root/.ssh:ro --rm hiranp/ansible-toolset ansible-playbook playbook.yml
````

Another options is to create aliases:

```bash
alias ansible='docker run -v "${PWD}":/workspace:ro --rm hiranp/ansible-toolset'
alias ansible-playbook='docker run -v "${PWD}":/workspace:ro -v ~/.ansible/roles:/root/.ansible/roles -v ~/.ssh:/root/.ssh:ro --rm hiranp/ansible-toolset ansible-playbook'
alias ansible-vault='docker run -v "${PWD}":/workspace:ro --rm hiranp/ansible-toolset ansible-vault'
```

**NOTE:** For direct Git access to local git repo

```bash
docker run -e GIT_DISCOVERY_ACROSS_FILESYSTEM=1 -v "${PWD}":/workspace:ro --rm hiranp/ansible-toolset
```

#### Visual Studio Code devcontainer

1. Install and configure the [Visual Studio Code Remote - Containers](https://code.visualstudio.com/docs/remote/containers) extension
1. Copy ``devcontainer.json`` to ``.vscode\devcontainer.json`` or ``.devcontainer.json``
1. Update and customise as required

#### CI/CD

Below is a basic example for use with ``gitlab-ci`` but this should be transferable to other providers.

```yaml
# .gitlab-ci.yml

---
image: hiranp/ansible-toolset

stages:
  - test
  - deploy

lint:
  stage: test
  script:
    - ansible-lint .

deploy:
  stage: deploy
  script:
    - ansible-playbook playbook.yml
...
```
