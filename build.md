# The Compose Specification - Build support
version: 3.9

*Note:* Build is an OPTIONAL part of the Compose Specification


## Introduction

Compose specification is a platform-neutral way to define multi-container applications. A Compose implementation focussing on development use-case to run application on local machine will obviously also support (re)building application from sources. The Compose Build specification allows to define the build process within a Compose file in a portable way.

## Definitions

Compose Specification is extended to support an OPTIONAL `Build` subsection on services. This section define the build requirements for service container image. Only a subset of Compose file services MAY define such a Build subsection, others being created based on `Image` attribute. When a Build subsection is present for a service, it is *valid* for a Compose file to miss an `Image` attribute for corresponding service, as Compose implementation can build image from source.

Build can be either specified as a single string defining a context path, or as a detailled build definition. 

In the former case, the whole path is used as a Docker context to execute a docker build, looking for a canonical `Dockerfile` at context root. Context path can be absolute or relative, and if so relative path MUST be resolved from Compose file parent folder. As an absolute path prevent the Compose file to be portable, Compose implementation SHOULD warn user accordingly.

In the later case, build arguments can be specified, including an alternate `Dockerfile` location. This one can be absolute or relative path. If Dockerfile path is relative, it MUST be resolved from context path.  As an absolute path prevent the Compose file to be portable, Compose implementation SHOULD warn user if an absolute alternate Dockerfile path is used.


## Consistency with Image

When service definition do include both `Image` attribute and a `Build` section, Compose implementation can't guarantee a pulled image is strictly equivalent to building the same image from sources. Without any explicit user directives, Compose implementation with Build support MUST first try to pull Image, then build from source if image was not found on registry. Compose implementation MAY offer options to customize this behaviour by user request.

## Publishing built images

Compose implementation with Build support SHOULD offer an option to push built images to a registry. Doing so, it MUST NOT try to push service images without an `Image` attribute. Compose implementation SHOULD warn user about missing `Image` attribute which prevent image being pushed.

Compose implementation MAY offer a mechanism to compute an `Image` attribute for service when not explicitly declared in yaml file. In such a case, the resulting Compose configuration is considered to have a valid `Image` attribute, whenever the actual raw yaml file doesn't explicitly declare one.


## Illustration sample

The following sample illustrates Compose specification concepts with a concrete sample application. The sample is non-normative.

```yaml
version: "3"
services:
  frontend:
    image: awesome/webapp
    build: ./webapp

  backend:
    image: awesome/database
    build:
        context: backend
        dockerfile: ../backend.Dockerfile

  custom:
    build: ~/custom
```

When used to build service images from source, such a Compose file will create three docker images: 

* `awesome/webapp` docker image is build using `webapp` sub-directory within Compose file parent folder as docker build context. Lack of a `Dockerfile` within this folder will throw an error.
* `awesome/database` docker image is build using `backend` sub-directory within Compose file parent folder. `backend.Dockerfile` file is used to define build steps, this file is searched relative to context path, which means for this sample `..` will resolve to Compose file parent folder, so `backend.Dockerfile` is a sibling file.
* a docker image is build using `custom` directory within user's HOME as docker context. Compose implementation warn user about non-portable path used to build image.


On push, both `awesome/webapp` and `awesome/database` docker images are pushed to (default) registry. `custom` service image is skipped as no `Image` attribute is set and user is warned about this missing attribute.


## Implementations

* [docker-compose](https://docs.docker.com/compose)
* [buildX bake](https://docs.docker.com/buildx/working-with-buildx/)
