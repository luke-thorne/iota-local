- [Local GoShimmer](#local-goshimmer)
- [Base components](#base-components)
	- [Peer master](#peer-master)
		- [Configuration](#configuration)
		- [Example](#example)
		- [Ports](#ports)
	- [Peer replicas](#peer-replicas)
		- [Configuration](#configuration-1)
		- [Example](#example-1)
		- [Ports](#ports-1)
	- [Faucet](#faucet)
		- [Configuration](#configuration-2)
		- [Example](#example-2)
		- [Ports](#ports-2)
- [Optional components](#optional-components)
	- [Grafana + Prometheus + MongoDB](#grafana--prometheus--mongodb)
		- [Profile](#profile)
		- [Configuration](#configuration-3)
		- [Example](#example-3)
		- [Ports](#ports-3)
	- [DRNG](#drng)
		- [Profile](#profile-1)
		- [Configuration](#configuration-4)
		- [Example](#example-4)
		- [Ports](#ports-4)

# Local GoShimmer

Create a local [GoShimmer](https://goshimmer.docs.iota.org/docs/welcome) network leveraging [Docker Compose](https://docs.docker.com/compose/compose-file/) with optional addons using [Docker Compose profiles](https://docs.docker.com/compose/compose-file/compose-file-v3/#profiles). There are several ways to configure this according to your needs as outlined below.

# Base components

These services that are created by default with `docker-compose up -d`.

## Peer master

A node that is used to expose ports via the host and to have a single attachment point for monitoring tools.

### Configuration

- MONGO_DB_ENABLED: Determines if the analysis tools should use a MongoDB instance for storing analysis data. Defaults to `false`. **Required if using [`--profile grafana`](#grafana)**.
- MESSAGE_SNAPSHOT_FILE: The full path to the message snapshot file. Defaults to `./goshimmer/assets/7R1itJx5hVuo9w9hjg5cwKFmek4HMSoBDgJZN8hKGxih.bin`
- GOSHIMMER_TAG: (Optional) The [iotaledger/goshimmer](https://hub.docker.com/r/iotaledger/goshimmer) tag to use. Defaults to `develop`.

### Example

You can set the environment variable configuration inline as seen in this example.

```bash
GOSHIMMER_TAG=develop docker-compose up -d
```

### Ports

The following ports are exposed on the host to allow for interacting with the Tangle.

| Port | Service |
|------|---------|
| 8080/tcp | Web API | 
| 9000/tcp | Analysis dashboard | 

## Peer replicas

A node that can be replicated to add more nodes to your local tangle.

### Configuration

- MESSAGE_SNAPSHOT_FILE: The full path to the message snapshot file. Defaults to `./goshimmer/assets/7R1itJx5hVuo9w9hjg5cwKFmek4HMSoBDgJZN8hKGxih.bin`
- GOSHIMMER_TAG: (Optional) The [iotaledger/goshimmer](https://hub.docker.com/r/iotaledger/goshimmer) tag to use. Defaults to `develop`.

### Example

You can set the environment variable configuration inline as seen in this example.

```bash
GOSHIMMER_TAG=develop docker-compose up -d
```

### Ports

These expose 0 ports because they are replicas and the host system cannot map a port to multiple containers.

## Faucet

A node that can dispense tokens.

### Configuration

- MESSAGE_SNAPSHOT_FILE: The full path to the message snapshot file. Defaults to `./goshimmer/assets/7R1itJx5hVuo9w9hjg5cwKFmek4HMSoBDgJZN8hKGxih.bin`
- GOSHIMMER_TAG: (Optional) The [iotaledger/goshimmer](https://hub.docker.com/r/iotaledger/goshimmer) tag to use. Defaults to `develop`.

### Example

You can set the environment variable configuration inline as seen in this example.

```bash
GOSHIMMER_TAG=develop docker-compose up -d
```

### Ports

The following ports are exposed on the host to allow for interacting with the Tangle.

| Port | Service |
|------|---------|
| 8081/tcp | Dashboard | 
<!-- The dashboard has issues displaying on the master peer when the 2.0 DevNet dashboard is running so we display the old dashboard on the faucet -->

# Optional components

These services can be added to your deployment through `--profile` flags and can be configured with `ENVIRONMENT_VARIABLES`.

## Grafana + Prometheus + MongoDB

A set of containers to enable dashboards and monitoring.

### Profile

In order to enable these containers you must set the `--profile grafana` flag when running `docker-compose`.

### Configuration

- MONGO_DB_ENABLED: __(REQUIRED)__ Determines if the analysis tools should use a MongoDB instance for storing analysis data and configures the `peer_master` to use MongoDB for analysis storage. Defaults to `false`.

### Example

You can set the environment variable configuration inline as seen in this example.

```bash
MONGO_DB_ENABLED=true docker-compose --profile grafana up -d
```

### Ports

The following ports are exposed on the host to allow for interacting with the Tangle.

| Port | Service |
|------|---------|
| 3000/tcp | Grafana | 
| 9090/tcp | Prometheus | 

## DRNG

Distributed randomness beacon.
Verifiable, unpredictable and unbiased random numbers as a service.

### Profile

In order to enable these containers you must set the `--profile drng` flag when running `docker-compose`.

### Configuration

- DRNG_REPLICAS: (Optional) How many nodes to create in addition to the DRNG leader. Defaults to 2.

### Example

You can set the environment variable configuration inline as seen in this example.

```bash
docker-compose --profile drng up -d
```

### Ports

The following ports are exposed on the host to allow for interacting with the Tangle.

| Port | Service |
|------|---------|
| 8000/tcp | Drand Control | 
| 8800/tcp | GoShimmer API | 
