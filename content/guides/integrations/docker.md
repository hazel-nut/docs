---
description: How to integrate W&B with Docker.
menu:
  default:
    identifier: docker
    parent: integrations
title: Docker
weight: 80
---

## Docker Integration

W&B can store a pointer to the Docker image that your code ran in, giving you the ability to restore a previous experiment to the exact environment it was run in. The wandb library looks for the **WANDB_DOCKER** environment variable to persist this state. We provide a few helpers that automatically set this state.

### Local Development

`wandb docker` is a command that starts a docker container, passes in wandb environment variables, mounts your code, and ensures wandb is installed. By default the command uses a docker image with TensorFlow, PyTorch, Keras, and Jupyter installed. You can use the same command to start your own docker image: `wandb docker my/image:latest`. The command mounts the current directory into the "/app" directory of the container, you can change this with the "--dir" flag.

### Production

The `wandb docker-run` command is provided for production workloads. It's meant to be a drop in replacement for `nvidia-docker`. It's a simple wrapper to the `docker run` command that adds your credentials and the **WANDB_DOCKER** environment variable to the call. If you do not pass the "--runtime" flag and `nvidia-docker` is available on the machine, this also ensures the runtime is set to nvidia.

### Kubernetes

If you run your training workloads in Kubernetes and the k8s API is exposed to your pod \(which is the case by default\). wandb will query the API for the digest of the docker image and automatically set the **WANDB_DOCKER** environment variable.

## Restoring

If a run was instrumented with the **WANDB_DOCKER** environment variable, calling `wandb restore username/project:run_id` will checkout a new branch restoring your code then launch the exact docker image used for training pre-populated with the original command.