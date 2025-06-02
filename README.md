# Homelab

This repo contains all the source and configs required to run my homelab Kubernetes cluster.

The idea is to showcase my approach to structuring and maintaining a secure Kubernetes cluster.

It is a sandbox for me to learn new cloud-native tools and technologies while being able to put them to practice.

`GitOps` is achieved through `FluxCD`.

I also use this cluster to maintain `devpods` for my other labs dedicated to application and systems tech, such as `dotnet` and `Open Telemetry`.

## Infancy

I am still in the early stages of bootstrapping architectural components such as a container registry, persistent storage, Ingress, CNI and secrets.

## Infrastructure

All nodes are running `ubuntu server 24.04 LTS`.

I used [k3s-ansible](https://github.com/k3s/k3s-ansible) to provision the cluster.

All default options were used as I will likely be using `external-dns` and `Cilium` later on.

> I might bring it in as a submodule later if I end up re-installing often.

## Hardware

Currently my homelab runs in a 4-node setup:
1 x control-plane
3 x workers

## Roadmap

Here is a list of things I want to achieve:

### Checklist

- [] Complete devpod integration
- [] Self-hosted storage
- [] Deploy self-hosted apps
- [] Azure secrets
- [] Expose apps to internet
- [] Container registry
- [] CNI
- [] Observability

### Milestones

#### Complete Devpod Integration

I want to be able to create `devpods` for my lab and apps.

I will need to implement resource constraints so they do not impact the rest of the cluster.

#### Self-Hosted Storage

Obtain a Synology NAS so my homelab can use persistent storage.

#### Deploy Self-Hosted Apps

Such as [commafeed](https://www.commafeed.com/#/welcome), [linkding](https://github.com/sissbruecker/linkding) and [wallabag](https://wallabag.org/).

#### Azure Secrets

I want to use [external-secrets](https://external-secrets.io/latest/) to connect to an `Azure Key Vault`.

This will allow me to

#### Expose Apps to Internet

Ensure use of industry standard best practices to secure my homelab cluster.

This will also allow me to access my self-hosted apps over the internet.

#### Container Registry

Self-hosting a container registry will allow me to create container images for my `dotnet` apps.

#### CNI

I want to use `Cilium` for my `CNI`.

Research indicates it is a robust widely-used tool.

#### Observability

Through a `LGTM/P + otel-collector` stack.

I enjoyed wiring `Open Telemetry` into `dotnet` apps, and I would like to continue exploring the tech.
