# Hyperledger Fabric Car Supply Chain

## Project Layout
This repository is organized around two operating modes so you can learn and run the same business flow in both local and distributed environments.

1. [single-host/](./single-host/) represents the Phase 1 flow and is intended for one-machine execution with `localhost`, including 2 orgs plus 1 customer org.
2. [multi-host/](./multi-host/) represents the Phase 2 flow and targets a 5-machine ZeroTier deployment with routable hostnames across nodes.
3. [chaincode-go/](./chaincode-go/) is the common smart-contract source used by both topologies, keeping the business logic identical across both modes.

## Getting Started

### Option 1: Single Machine (Phase 1)
Use this route when everything will run on one computer.

1. Change into the single-host setup folder.
2. Launch network creation and channel setup using the bundled script.
3. Continue with the Phase 1 documentation index.

```bash
$ cd ./single-host
$ ./network.sh up createChannel -c supplychain
```

Documentation entry point: [single-host/docs/README.md](./single-host/docs/README.md)

### Option 2: Distributed 5-PC (Phase 2)
Use this route when you are moving to the multi-node ZeroTier setup.

1. Enter the multi-host setup folder.
2. Follow the external phase-2 migration runbook.
3. Run the workspace-level `check_prerequisites.sh` helper before proceeding.

```bash
$ cd ./multi-host
```

Phase-2 runbook: [/home/dishant/HyperledgerFabric/plan/phase2/README.md](/home/dishant/HyperledgerFabric/plan/phase2/README.md)

## Workspace-Level Planning Notes
Operational planning documents for this workspace are intentionally maintained outside this Git repository at [/home/dishant/HyperledgerFabric/plan/](/home/dishant/HyperledgerFabric/plan/).

## Running the Fabric Test Network
Use `./network.sh` to bootstrap the sample Fabric network, create channels, and deploy chaincode.

The default network model includes two peer organizations (one peer each) and a single-node Raft ordering service. Background and conceptual details are available in the official guide: [Using the Fabric test network](https://hyperledger-fabric.readthedocs.io/en/latest/test_network.html).

This test-network model was introduced in Fabric v2.0 as the long-term successor to `first-network`.

## BFT Mode Commands
If you want BFT consensus, include the `-bft` flag when handling channel creation workflows. This sample also supports combining BFT mode with CA-based crypto generation.

Create the network in BFT mode:

```bash
$ ./network.sh up -bft
```

Create the channel in BFT mode:

```bash
$ ./network.sh createChannel -bft
```

Restart an existing BFT-mode network:

```bash
$ ./network.sh restart -bft
```

Running `createChannel` will also start the network automatically when it is not already up.

Before launching this network, complete Fabric's official install flow for sample assets, binaries, and Docker images: [Install the Samples, Binaries and Docker Images](https://hyperledger-fabric.readthedocs.io/en/latest/install.html).

## Direct Peer CLI Usage
The `setOrgEnv.sh` helper emits organization-specific environment variables so you can execute `peer` commands directly in the right org context.

From the `test-network` directory, make sure your binary path and Fabric config path are exported first:

```bash
$ export PATH="${PATH}:$(realpath ./bin)"
$ export FABRIC_CFG_PATH="$(realpath ./config)"
```

Then load an org context (example: Showroom):

```bash
$ export $(./setOrgEnv.sh Showroom | xargs)
```

Script requirement: Bash v4.

After exporting that context, `peer` commands run as Showroom. To switch contexts, run the same command with Manufacturer instead.

The `setOrgEnv` output format is `<name>=<value>`, which is why piping to `export ... | xargs` works for shell setup.

## Chaincode as a Service
For chaincode-as-a-service improvements, use the tutorial here: [CHAINCODE_AS_A_SERVICE_TUTORIAL](./docs/CHAINCODE_AS_A_SERVICE_TUTORIAL.md).

Over time, this material is expected to be folded into the official docs: [Hyperledger Fabric ReadTheDocs](https://hyperledger-fabric.readthedocs.io/en/release-2.4/cc_service.html).

## Podman Support (Experimental)
Podman support is currently considered experimental. Reported working baseline includes podman 4.1.1 on Mac, and a Linux VM is the recommended option when using podman.

The `install-fabric.sh` flow can use podman to pull and tag images instead of Docker. The resulting images are the same; only the pull/tag mechanism changes. To do that, pass `podman` when running `install-fabric.sh`.

Likewise, `network.sh` can run with `podman` and `podman-compose` by setting `CONTAINER_CLI=podman`:

```bash
$ CONTAINER_CLI=podman ./network.sh up
```

Because podman does not provide a Docker daemon, only `./network.sh deployCCAAS` is supported in this mode. The chaincode-as-a-service tutorial above remains the recommended path.


