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

I used [k3s-ansible](https://github.com/k3s-io/k3s-ansible) to provision the cluster.

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
- [x] Self-hosted storage
- [x] Deploy self-hosted apps
- [x] Azure secrets
- [x] Expose apps to internet
- [] Container registry
- [x] CNI
- [] Observability

### Milestones

These milestones describe the items in the checklist.

#### Complete Devpod Integration

I want to be able to create `devpods` for my lab and apps.

I will need to implement resource constraints so they do not impact the rest of the cluster.

##### UPDATE

There a two main bottlenecks for getting this online:
1. Synology LUN limit of the DS118. I need to upgrade, probably to a DS224+.
2. Devpod takes a very long time to pull the images because of ISP bandwidth and registry rate limiting. Solved by implementing my own pull-through cache.

#### Self-Hosted Storage

Obtain a Synology NAS so my homelab can use persistent storage.

> COMPLETED

#### Deploy Self-Hosted Apps

Such as [commafeed](https://www.commafeed.com/#/welcome), [linkding](https://github.com/sissbruecker/linkding) and [wallabag](https://wallabag.org/).

> COMPLETED

#### Azure Secrets

I want to use [external-secrets](https://external-secrets.io/latest/) to connect to an `Azure Key Vault`.

This will allow me to securely and privately store my app and service credentials.

> COMPLETED

#### Expose Apps to Internet

Ensure use of industry standard best practices to secure my homelab cluster.

This will also allow me to access my self-hosted apps over the internet.

> COMPLETED

#### Container Registry

Self-hosting a container registry will allow me to create container images for my `dotnet` apps.

It would also allow me to set up some pull-through caches to reduce devpod creation times.

#### CNI

I want to use `Cilium` for my `CNI`.

Research indicates it is a robust widely-used tool.

> COMPLETED

#### Observability

Through a `LGTM/P + otel-collector` stack.

I enjoyed wiring `Open Telemetry` into `dotnet` apps, and I would like to continue exploring the tech.

They key challenge here is ensuring the collector and Prometheus play well together.

Prometheus will need the `remote-receiver` (or equivelant flag) enabling for collectors to push metrics to it.

An amount of relabelling will need to be done for the dashboards to work.
