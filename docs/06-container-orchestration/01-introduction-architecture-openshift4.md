# Introduction & Architecture Openshift Container Platform 4


## Limitations of Containers

As the number of containers managed by an organization grows, the work of manually starting them rises exponentially. When using containers in a production environment, enterprises often require:

* Easy communication between a large number of services.
* Resource limits on applications regardless of the number of containers running them.
* Responses to application usage spikes to increase or decrease running containers.
* Reaction to service deterioration.
* Gradual roll-out of new release to a set of users.

Enterprises often require a container orchestration technology because container runtimes (such as Podman) do not adequately address the above requirements.

## Kubernetes Overview

Kubernetes is an orchestration service that simplifies the deployment, management, and scaling of containerized applications.

The smallest unit manageable in Kubernetes is a pod. A pod consists of one or more containers with its storage resources and IP address that represent a single application.

## Kubernetes Features

Kubernetes offers the following features on top of a container infrastructure:

