# k8s — Cassandra pod manifest for the market-data stack

A single Kubernetes pod manifest, captured from the local development Cassandra container that
backs the owner's market-research web app (the `herrrickshaw` keyspace of stock instruments and
quotes). It is the starting point for moving that container from Podman on a laptop to a
Kubernetes host. Nothing else lives here.

> Status: archived snapshot. The manifest was generated with `podman generate kube` on 2026-08-05
> and is not deployable as-is (see Limitations).

> **New here?** See [How to use this repo with AI (Claude, Gemini, ChatGPT)](HOW_TO_USE_WITH_AI.md).

## What it does
- Describes one pod, `cassandra-market-pod`, running `docker.io/library/cassandra:4.1` with
  `cassandra -f` as the container command.
- Sets `CASSANDRA_CLUSTER_NAME=herrrickshaw` and `CASSANDRA_DC=datacenter1`.
- Exposes ports 7199 (JMX) and 9042 (CQL native) as host ports.
- Mounts `/var/lib/cassandra` from a PersistentVolumeClaim named `cassandra-market-data`.

## Quick start
```bash
git clone https://github.com/herrrickshaw/k8s.git
cd k8s
# define a PVC named cassandra-market-data first, then:
kubectl create -f cassandra-market-pod.yaml
```

## Repository layout

| Path | Purpose |
|---|---|
| `cassandra-market-pod.yaml` | The pod manifest (Podman 6.0.2 `generate kube` output plus a header comment) |
| `HOW_TO_USE_WITH_AI.md` | How to read the repo with an AI assistant |
| `graphify-out/` | Generated knowledge-graph cache for the `graphify` tool; not hand-edited |
| `.github/workflows/quality-gates.yml` | gitleaks secret scan on push / PR |

## Configuration
The two environment variables are set inline in the manifest. No secrets are involved; the image
is the public Cassandra 4.1 image with default authentication.

## Limitations and known issues
- No `PersistentVolumeClaim` or `StorageClass` object is defined, so `kubectl create` fails until
  `cassandra-market-data` exists.
- `hostPort` mappings and the single-`Pod` kind are Podman conveniences; a real deployment would use
  a `StatefulSet` and `Service`.
- The header references `PHASE4_DEPLOYMENT_CONFIGURATION.md`, which lives in the web-app repository,
  not here.
- Generated from a rootless Podman container; on SELinux hosts see the `podman generate kube` man
  page for volume permissions.

## Related repositories
The application this database serves is the owner's market-research FastAPI + React app, whose
`db/cassandra_client.py` connects to port 9042 on the `herrrickshaw` keyspace.

## Licence
No licence file yet — all rights reserved.
