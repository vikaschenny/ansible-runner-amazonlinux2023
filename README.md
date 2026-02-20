
## Ansible Runner image (Amazon Linux 2023)

This document describes how to build the `ansible-runner` container image
using the custom Ansible Builder image:

- **Base image**: `vikaschenny/ansible-builder:amazonlinux2023`

The result is a simple image suitable for testing that has `ansible-core`
installed and a pre-created `/runner` environment.

---

### Dockerfile overview

The `Dockerfile` in this directory contains:

- `FROM vikaschenny/ansible-builder:amazonlinux2023`
- `ARG WHEEL` (optional build argument; currently unused in the standard build)
- Installs `python3-pip`
- Installs `ansible-core` via `pip`
- Creates the runner directories:
  - `/runner/{env,inventory,project,artifacts}`
  - `/home/runner/.ansible/tmp`
- Sets permissions on `/runner` and `/home/runner`
- Sets:
  - `WORKDIR /runner`
  - `ENV HOME=/home/runner`
- Default command:
  - `["ansible", "--version"]`

---

### Build locally

From the `ansible-runner` directory (next to `Dockerfile`):

```bash
cd ansible-runner

# Simple build (no wheel argument)
docker build -f Dockerfile -t ansible-runner:latest .

# Optionally, pass a wheel file if you want to copy it into the image
docker build \
  -f Dockerfile \
  --build-arg WHEEL=dist/ansible_runner-<version>-py3-none-any.whl \
  -t ansible-runner:latest .
```

This will:

- Pull `vikaschenny/ansible-builder:amazonlinux2023`
- Install `python3-pip` and `ansible-core`
- Prepare `/runner` as the working directory

---

### Build on the MCP server

On the MCP server (where the repo is cloned as `~/ansible-runner`
and the base image is already present):

```bash
cd ~/ansible-runner
docker build -f Dockerfile -t ansible-runner:latest .
```

Verify the image:

```bash
docker images ansible-runner:latest
```

You should see `ansible-runner:latest` with an Amazon Linux 2023–based
stack via `vikaschenny/ansible-builder:amazonlinux2023`.

---

### Running the image

To quickly verify the container:

```bash
docker run --rm ansible-runner:latest
```

You should see the `ansible --version` output printed from inside
the container.



########################### OLD ONE ##########################
Ansible Runner
==============

[![PyPi](https://img.shields.io/pypi/v/ansible-runner.svg?logo=Python)](https://pypi.org/project/ansible-runner/)
[![Documentation](https://readthedocs.org/projects/ansible-runner/badge/?version=stable)](https://docs.ansible.com/projects/runner/en/latest/)
[![Code of Conduct](https://img.shields.io/badge/Code%20of%20Conduct-Ansible-silver.svg)](https://docs.ansible.com/projects/ansible/latest/community/code_of_conduct.html)
[![codecov](https://codecov.io/gh/ansible/ansible-runner/branch/devel/graph/badge.svg?token=CmCcjBz0pQ)](https://codecov.io/gh/ansible/ansible-runner)

Ansible Runner is a tool and Python library that helps when interfacing with Ansible directly or as part of another system. Ansible Runner works as a standalone tool, a container image interface, or a Python module that can be imported. The goal is to provide a stable and consistent interface abstraction to Ansible.

See the [latest documentation] for usage details.

Get Involved
------------

* [GitHub issues] to track bug report and feature ideas
* [GitHub Milestones] to track what's for the next release
* Want to contribute? Please check out our [contributing guide]
* Visit the [Community] section of the docs.
* See the [Ansible communication guide] for complete information about getting in touch.

[GitHub issues]: https://github.com/ansible/ansible-runner/issues
[GitHub Milestones]: https://github.com/ansible/ansible-runner/milestones
[contributing guide]: https://github.com/ansible/ansible-runner/blob/devel/CONTRIBUTING.md
[Community]: https://docs.ansible.com/projects/runner/en/latest/community/
[Ansible communication guide]: https://docs.ansible.com/projects/ansible/devel/community/communication.html
[latest documentation]: https://docs.ansible.com/projects/runner/en/latest/
