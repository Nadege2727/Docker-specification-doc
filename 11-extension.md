## Extension

As with [Fragments](10-fragments.md), Extensions can be used to make your Compose file more efficient and easier to maintain. Extensions can also be used with [anchors and aliases](10-fragments.md).

Use the prefix `x-` on any top-level element to modularize configurations that you want to reuse. They can be used
within any structure in a Compose file as Docker Compose ignores any fields that start with `x-`.  This is the sole exception for Compose implementations to silently ignore unrecognized fields.

The contents of any `x-` section is unspecified by Compose specification, so it can be used to enable custom features. If the compose implementation encounters an unknown extension field it MUST NOT fail, but COULD warn the user about the unknown field.

### Example 1

```yml
x-custom:
  foo:
    - bar
    - zot

services:
  webapp:
    image: awesome/webapp
    x-foo: bar
```

For platform extensions, it is highly recommended to prefix extension by platform/vendor name, the same way browsers add
support for [custom CSS features](https://www.w3.org/TR/2011/REC-CSS2-20110607/syndata.html#vendor-keywords)

```yml
service:
  backend:
    deploy:
      placement:
        x-aws-role: "arn:aws:iam::XXXXXXXXXXXX:role/foo"
        x-aws-region: "eu-west-3"
        x-azure-region: "france-central"
```

### Example 2

```yml
x-env: &env
  environment:
    - CONFIG_KEY
    - EXAMPLE_KEY
 
services:
  first:
    <<: *env
    image: my-image:latest
  second:
    <<: *env
    image: another-image:latest
```

In this example, the environment variables do not belong to either of the services. They’ve been lifted out completely, into the `x-env` extension field.
This defines a new node which contains the environment field. A YAML anchor is used (`&env`) so both services can reference the extension field’s value as `*env`.

### Example 3

```yml
x-function: &function
 labels:
   function: "true"
 depends_on:
   - gateway
 networks:
   - functions
 deploy:
   placement:
     constraints:
       - 'node.platform.os == linux'
services:
 # Node.js gives OS info about the node (Host)
 nodeinfo:
   <<: *function
   image: functions/nodeinfo:latest
   environment:
     no_proxy: "gateway"
     https_proxy: $https_proxy
 # Uses `cat` to echo back response, fastest function to execute.
 echoit:
   <<: *function
   image: functions/alpine:health
   environment:
     fprocess: "cat"
     no_proxy: "gateway"
     https_proxy: $https_proxy
```

The `nodeinfo` and `echoit` services both use merge it in, then set their specific image and environment. 

### Informative Historical Notes

This section is informative. At the time of writing, the following prefixes are known to exist:

| prefix     | vendor/organization |
| ---------- | ------------------- |
| docker     | Docker              |
| kubernetes | Kubernetes          |

### Specifying byte values

Values express a byte value as a string in `{amount}{byte unit}` format:
The supported units are `b` (bytes), `k` or `kb` (kilo bytes), `m` or `mb` (mega bytes) and `g` or `gb` (giga bytes).

```
    2b
    1024kb
    2048k
    300m
    1gb
```

### Specifying durations

Values express a duration as a string in the form of `{value}{unit}`.
The supported units are `us` (microseconds), `ms` (milliseconds), `s` (seconds), `m` (minutes) and `h` (hours).
Values can combine multiple values without separator.

```
  10ms
  40s
  1m30s
  1h5m30s20ms
```
