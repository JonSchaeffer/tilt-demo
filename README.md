# Running this app with Tilt 

This example voting app was forked from [dockersamples/example-voting-app](https://github.com/dockersamples/example-voting-app) and modified to briefly showcase tilt.

Tilt is a tool that allows you to develop your Kubernetes applications locally. It is a great tool for developing and debugging your applications in a local Kubernetes cluster.

I added the following files for Tilt to operate:
- `Tilefile`
- `k3d-cluster.yaml`
- `k8s/specifications/seed-job.yaml`
- `vote/Dockerfile` (This needed edited because one command was out of order)

### Required Dependencies

- k3d
- helm
- tilt

To install for mac: `brew install k3d helm tilt`

### Optional Dependencies (But highly encouraged)

- k9s
- kubectx

To install for mac : `brew install k9s kubectx`

To install the whole thing: `brew install k3d helm tilt k9s kubectx`

## Running Tilt

Provided you have the required dependencies installed and you are in the root of the repo, you can run the below commands to get started with Tilt

`k3d cluster create --config k3d-cluster.yaml`

Note: This command only needs to be run once. If you delete the k3d containers out of Docker, do a docker purge, or docker isn't showing any containers, you will need to run the above command again.

Once the k3d cluster has been created, then you can run from the mother repo's root dir:
`tilt up`

Note: `tilt up` can only be run from the repo's root dir.


## Viewing pods in tilt

If you have installed k9s and kubectx, you can run these commands to view the local cluster.

`kubectx k3d-tilt`
`k9s`

This will drop you into k9s, a graphical tool for view Kubernetes clusters. You can read more about the tool [here](https://k9scli.io/).

To view pods, you can enter on your keyboard `:pods` + `enter`. You can navigate up and down using vim keystrokes. When highlighted on a pod, press `l` to view the logs. `Esc` will get you back. In that same vein, `s` when a pod is highlighted will pop you in a shell session for that pod.

## Pausing the k3d cluster

If you want to bring your cluster down for some reason, you can do that and resume it later. 

1. If `tilt` is running, stop it with `ctrl + c` (from the terminal its running) THEN
2. From the terminal run `k3d cluster stop tilt`

## Resuming the k3d cluster

1. From the terminal run `k3d cluster start tilt`
2. From the repo's root dir, run `tilt up`

This will bring tilt back up, and tilt will re-attach to the running resources.

## Deleting the k3d cluster

If you need to completely remove the k3d cluster, you can do the following

1. From the terminal, run `k3d cluster delete tilt`

If you need to bring the cluster back up, from the repo's root dir, run `k3d cluster create --config k3d-cluster.yaml`

---

# Example Voting App

A simple distributed application running across multiple Docker containers.

## Getting started

Download [Docker Desktop](https://www.docker.com/products/docker-desktop) for Mac or Windows. [Docker Compose](https://docs.docker.com/compose) will be automatically installed. On Linux, make sure you have the latest version of [Compose](https://docs.docker.com/compose/install/).

This solution uses Python, Node.js, .NET, with Redis for messaging and Postgres for storage.

Run in this directory to build and run the app:

```shell
docker compose up
```

The `vote` app will be running at [http://localhost:8080](http://localhost:8080), and the `results` will be at [http://localhost:8081](http://localhost:8081).

Alternately, if you want to run it on a [Docker Swarm](https://docs.docker.com/engine/swarm/), first make sure you have a swarm. If you don't, run:

```shell
docker swarm init
```

Once you have your swarm, in this directory run:

```shell
docker stack deploy --compose-file docker-stack.yml vote
```

## Run the app in Kubernetes

The folder k8s-specifications contains the YAML specifications of the Voting App's services.

Run the following command to create the deployments and services. Note it will create these resources in your current namespace (`default` if you haven't changed it.)

```shell
kubectl create -f k8s-specifications/
```

The `vote` web app is then available on port 31000 on each host of the cluster, the `result` web app is available on port 31001.

To remove them, run:

```shell
kubectl delete -f k8s-specifications/
```

## Architecture

![Architecture diagram](architecture.excalidraw.png)

* A front-end web app in [Python](/vote) which lets you vote between two options
* A [Redis](https://hub.docker.com/_/redis/) which collects new votes
* A [.NET](/worker/) worker which consumes votes and stores them in…
* A [Postgres](https://hub.docker.com/_/postgres/) database backed by a Docker volume
* A [Node.js](/result) web app which shows the results of the voting in real time

## Notes

The voting application only accepts one vote per client browser. It does not register additional votes if a vote has already been submitted from a client.

This isn't an example of a properly architected perfectly designed distributed app... it's just a simple
example of the various types of pieces and languages you might see (queues, persistent data, etc), and how to
deal with them in Docker at a basic level.
