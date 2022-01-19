baschny/append-buildx-action
============================

This is a GitHub Action that allows to append external builders to the docker buildx.
This is meant to be called after `setup-buildx-action`.

Useful if you want to create cross-platform images and do not want to use the slow
QEMU approach which is used by default if there are not external builders configured.

With this "add-on" you can attach for example an external (i.e. AWS hosted) ARM instance
or use a Kubernetes cluster.

Input
-----

* `builder`: The builder name from the previous step.
* `platform`: For what platform to add the new node (defaults to `linux/arm64`)
* `node_name`: The name of the new node (defaults to `arm`)
* `driver_opt`: Some --driver-opt settings, for example `nodeselector=kubernetes.io/arch=arm64`
* `endpoint`: An endpoint/context for this node, i.e. `ssh://user@docker-instance.example.com`

If using an `ssh://` endpoint, make sure you set a secret in your repository containing
the private SSH key, called `SSH_PRIVATE_KEY`. You need SSH access to this docker machine
with its corresponding public key.

Example Workflow
----------------

```yaml
name: Build cross platform docker images

on: [push]

jobs:

  build:
    runs-on: ubuntu-latest

    steps:
      - name: "Checkout repository"
        uses: actions/checkout@v2
      
      - name: "Set up Docker Buildx"
        id: builder
        uses: docker/setup-buildx-action@v1

      - name: "Append ARM buildx builder from AWS"
        uses: baschny/append-buildx-action@v1
        with:
          builder: ${{ steps.builder.outputs.name }}
          endpoint: "ssh://ec2-user@arm-docker.example.com"
```

License
-------

See LICENSE file.

See also
--------

The idea came while waiting for this feature request:
https://github.com/docker/setup-buildx-action/issues/115.

Author
------

Ernesto Baschny, [cron IT GmbH](https://www.cron.eu)