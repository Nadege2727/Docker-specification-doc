# Compose Specification

The Compose specification establishes a standard for the definition of
multi-container platform-agnostic applications. The specification can be found
[here](spec.md).

![logo](logo.jpg)

## Table of Contents

Additional documentation about how this group operates:
* [Governance](GOVERNANCE.md)
* [Contribution Guidelines](CONTRIBUTING.md)
* [Implementations](#Implementations)
* [Releases](https://github.com/docker/compose-spec/releases)

## Use cases

To provide more context on the Compose specification the following section gives
example use cases for each part of the specification.

### Developement tools

Developers can use a Compose file to specify a container-based application
that will run as a set of containers on a local container engine. The Compose
implementation in this scenario could offer some specific features (local
volume binding, live-reload) to better address development needs. The
application definition and Compose model is the same used as that used for other
use cases. Platform features expected by the specification (like configs and
secrets) can be mocked with local resources.

### Kubernetes deployment

Kubernetes container orchestration relies on a set of abstract concepts and
APIs to manage networking services, container deployments and their lifecycles.
While this offers flexibility to address many operator use cases, it makes
simple use cases, like the developer use case, more complicated to express than
they need to be. Projects like [Kompose](https://github.com/kubernetes/kompose)
and [Compose on Kubernetes](https://github.com/docker/compose-on-kubernetes)
show how the simpler Compose model can be translated into Kubernetes API
payloads and make the Compose file the source of truth for development and
deployment.

### Cloud providers

Some cloud providers offer proprietary container hosting solutions based on
in-house orchestrators and custom APIs. The Compose specification offers a
simple model that can be mapped to these hosting solutions so that users can
reuse Compose files that they already have and so that they do not need to learn
custom configuration file formats. Platform specific features can be added
either using Compose extensions or a dedicated configuration file along side the
Compose file.

## Contributing

Development happens on GitHub for the specification. Issues are used to track
bugs and actionable items. Longer discussions can happen on the
[mailing list](https://groups.google.com/forum/#!forum/compose-spec).

The specification and code is licensed under the Apache 2.0 license found in the
[LICENSE](LICENSE) file.

## Implementations

* [docker-compose](https://github.com/docker/compose)
* Docker CLI (`docker stack` command)
* [Compose on Kubernetes](https://github.com/docker/compose-on-kubernetes)
* [Kompose](https://github.com/kubernetes/kompose)

| Metadata |                  |
| -------- | ---------------: |
| Version  | 3.9              |
| Status   | Work in progress |
| Created  | 2020-01-02       |
