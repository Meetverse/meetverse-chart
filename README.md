# Meetverse Kubernetes Chart

## Introduction
This Helm chart installs the Meetverse application in a Kubernetes cluster. The application architecture includes a frontend and a backend service, both of which share a single ingress controller. MongoDB and Qdrant are included as dependencies to manage data storage and vector search functionality, respectively.

## Prerequisites
- Kubernetes 1.19+
- Helm 3.0+
- PV provisioner support in the underlying infrastructure (for MongoDB persistence)

## Installing the Chart
To install the chart with the release name `meetverse`:

```bash
helm repo add meetverse https://meetverse.github.io/meetverse-chart/
helm install meetverse meetverse/meetverse
```

This command deploys Meetverse on the Kubernetes cluster with the default configuration. 
The Parameters section lists the parameters that can be configured during installation.

## Uninstalling the Chart
To uninstall/delete the my-meetverse deployment:

```bash
helm delete meetverse
```

## Configuration
The following table lists the required configurable parameters of the Meetverse chart and their default values.


| Parameter                     | Description                                   | Default                                                    |
|-------------------------------|-----------------------------------------------|------------------------------------------------------------|
| `frontend.resources`          | CPU/Memory resource requests/limits           | `{}` (see values.yaml)                                     |
| `frontend.image.repository`   | Frontend image repository                     | `us-west1-docker.pkg.dev/meetversetest/meetverse/web`      |
| `frontend.image.pullPolicy`   | Image pull policy                             | `IfNotPresent`                                             |
| `frontend.image.tag`          | Image tag                                     | `latest`                                                   |
| `frontend.service.type`       | Kubernetes Service type                       | `ClusterIP`                                                |
| `frontend.service.port`       | Service port                                  | `8080`                                                     |
| `replicaCount`                | Number of nodes                               | `1`                                                        |
| `image.repository`            | Backend image repository                      | `us-west1-docker.pkg.dev/meetversetest/meetverse/api`      |
| `serviceAccount.create`       | Specifies whether a service account should be created | `true`                        |
| `ingress.enabled`             | Enable ingress controller                     | `true`                                                     |
| `mongodb.service.nameOverride`| Override MongoDB service name                 | `mongodb`                                                  |
| `mongodb.auth.usernames`      | MongoDB usernames                             | `["mongo"]`                                                |
| `mongodb.auth.databases`      | MongoDB databases                             | `["meetverse"]`                                            |
| `mongodb.auth.existingSecret` | MongoDB existing secret                       | `meetverse`                                                |



### Dependencies

- [MongoDB (Bitnami MongoDB chart)](https://github.com/bitnami/charts/tree/main/bitnami/mongodb)

- [Qdrant](https://github.com/qdrant/qdrant-helm)

For more information on configuring the dependencies, refer to their respective Helm chart documentation. 

To use values from those charts just refer to them in the values as `mongodb` and `qdrant` respectivelly, eg:

```
mongodb:
  service:
    nameOverride: "mongodb"
  auth:
    usernames: ["mongo"]
    databases: ["meetverse"]
    existingSecret: meetverse
```

Those values will be used by the mongoDB chart.

This chart, using its default values, is expecting a nginx ingress controller and a cert-manager bot to be available in the cluster (one for routing, the 
other for automating ssl cert provisioning). You can use any other tool for those needs, just remember to customize the values before doing so.



### Customizing the Chart Before Installing

To edit the default configuration, use:

```bash
helm show values meetverse/meetverse > values.yaml
# edit values.yaml
helm install meetverse meetverse/meetverse -f values.yaml
```

## Updating chart

This is an automated chart repo. It provides the pkgs for charts using github pages and [chart releaser](https://github.com/helm/chart-releaser).

To create a new chart version, just make the changes you want on the chart code, then bump the version in ``Chart.yaml`` and push the changes to ``main`` branch. 
This will trigger the automation to create a new version of the chart and make it available for others to use.

This chart repo right now is PUBLIC, so take care to not provide any secrets in it, and rely on user provided secrets/variables.

You can of course do all the steps manully and push it to chart repo yourself, for this please check the [official helm docs](https://helm.sh/docs/).

## Adding new charts

Note that this repo is ready to host multiple charts, if you want to add a new one, just add it to `charts` folder. Once the chart is pushed, the automation will take care
of packaging and releasing it.

## GCP Market Place

One of the possible ways to deploy applications using GCP Marketplace is to deliver helm charts for the customer cluster. This repo contains all needed files and 
routines for that, one will just need to add the specific GCP files in this chart, and it will be ready to be used.
