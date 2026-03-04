# Kubernetes workshop
2023 Frameworks Onsite
---

## Architecture refresher
---
## Architecture refresher: Kubernetes clusters
------
```
┌───────────────────────────── cluster ─────────────────────────────┐
│                                                                   │
│  ┌─── node ────┐ ┌─── node ────┐ ┌─── node ────┐ ┌─── node ────┐  │
│  │             │ │ ┌───┐ ┌───┐ │ │             │ │       ┌───┐ │  │
│  │ ┌───┐       │ │ │pod│ │pod│ │ │             │ │       │pod│ │  │
│  │ │pod│  ┌───┐│ │ └───┘ └───┘ │ │  ┌───────┐  │ │       └───┘ │  │
│  │ └───┘  │pod││ │ ┌───┐       │ │  │  pod  │  │ │ ┌───┐       │  │
│  │        └───┘│ │ │pod│       │ │  └───────┘  │ │ │pod│       │  │
│  │             │ │ └───┘       │ │             │ │ └───┘       │  │
│  └─────────────┘ └─────────────┘ └─────────────┘ └─────────────┘  │
│                                                                   │
└───────────────────────────────────────────────────────────────────┘
```
---

## Architecture refresher: Kubernetes clusters
------
### Examining locally configured SKI clusters...

```bash
kubectl config view | yq '{"clusters": [.clusters[].name]}'
```
---

## Architecture refresher: Kubernetes nodes
------
```
┌─────────────────────────────── node ──────────────────────────────┐
│      ┌─────┐           ┌─────┐                       ┌─────┐      │
│      │ pod │  ┌─────┐  │ pod │     ┌─────┐   ┌─────┐ │ pod │      │
│      └─────┘  │ pod │  └─────┘     │ pod │   │ pod │ └─────┘      │
│               └─────┘      ┌─────┐ └─────┘   └─────┘              │
│ ┌────────────┐             │ pod │                    ┌─────────┐ │
│ │            │     ┌─────┐ └─────┘  ┌─────┐           │         │ │
│ │ containerd │     │ pod │          │ pod │  ┌─────┐  │ kubelet │ │
│ │            │     └─────┘ ┌─────┐  └─────┘  │ pod │  │         │ │
│ └────────────┘             │ pod │           └─────┘  └─────────┘ │
│                            └─────┘                                │
└───────────────────────────────────────────────────────────────────┘
```
---

## Architecture refresher: Kubernetes pods
------
```
            ┌─────────────────── pod ────────────────────┐
            │ ┌─────────────┐                            │
            │ │  sq-cloud-  │ - shared filesystem        │
            │ │    envoy    │ - shared process namespace │
            │ └─────────────┘ - shared network namespace │
            │    ┌───────┐                               │
            │    │ nginx │      ┌─────────┐              │
            │    └───────┘      │         │              │
            │ ┌────────────┐    │ sidekiq │              │
            │ │  Ruby app  │    │         │              │
            │ └────────────┘    └─────────┘              │
            └────────────────────────────────────────────┘
```
---

## Architecture refresher: Kubernetes containers
------
```
                 ┌────────── container ─────────┐
                 │                              │
                 │   - image                    │
                 │   - command (ENTRYPOINT)     │
                 │   - args (CMD)               │
                 │   - environment variables    │
                 │   - resource requests        │
                 │   - resource limits          │
                 │   - readiness probes         │
                 │   - liveness probes          │
                 │   - ...                      │
                 └──────────────────────────────┘
```
---

## Architecture refresher: Kubernetes control plane
------
```
┌ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ control plane ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ┐
                          ┌─────────────┐                 ┌──────┐   
│                         │    kube-    ├┐                │      │  │
                     ┌────│  apiserver  │├───────────────▶│ etcd │   
│  ┌─────────────┐   │    └┬────────────┘│                │      │  │
   │    kube-    │   │     └─────┬───────┘                └──────┘   
│  │ controller- │◀──┘           │      ┌─────────────┐             │
   │   manager   │               │      │    kube-    │              
│  └─────────────┘               └─────▶│  scheduler  │             │
                                        └─────────────┘              
│                                                                   │
 ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ 
```
---

## Workloads
---
## Workloads
------
### Overview
- Pod
- Deployment and ReplicaSet
- StatefulSet
- DaemonSet
- Job and CronJob
---

## Workloads: Deployment vs. ReplicaSet
-------------
### Deployment
- Describes the desired state of the workload
- Defines the update strategy for achieving the desired state
- Creates ReplicaSets and manages how pods are allocated between them
---

## Workloads: Deployment vs. ReplicaSet
------
### ReplicaSet
- Ensures a given number of pods are running
- Lower-level concept than a Deployment
- One potential use case: custom update orchestration
---

## Debugging
---
## Debugging
-------------
### Where am I?
```bash
sq ski context current
```
---

## Debugging
-------------
### Where am I?
```bash
kubectl config current-context
```
---

## Debugging
-------------
### Getting to where you want to be
```bash
sq ski context switch --help
```
---

## Debugging
-------------
### Getting to where you want to be
```bash
kubectl config set-context --help
```
---

## Debugging
-------------
### Don't have access?
- https://go/registry OR
- https://go/aws
---

## Debugging
-------------
### Don't have access?
```bash
sq ski context update --help
```
---

## Debugging
-------------
### Don't have access?
```bash
beyond-curl -s \
https://registry.sqprod.co/api/v1/user/ntarrh/aws_credentials \
| head -n 7
```
---

## Debugging
-------------
### Don't have access?
```bash
aws-creds get --help
```
---

## Debugging
-------------
### Don't have access?
```bash
aws-creds get 212513346311 read-only | jq '.'
```
---

## Debugging
-------------
### Using kubectl
- kubectl VERB NOUN [NAME] [args]
- kubectl get pods
- kubectl get pod NAME -o yaml
- kubectl describe deployment NAME

---

## Debugging
-------------
### Using kubectl: get vs describe
- kubectl get pod NAME -o yaml
- kubectl describe pod NAME
- helpful: pipe to vim (or your preferred $EDITOR)

---

## Debugging
-------------
### Logs
- kubectl logs POD [-c CONTAINER]
- kubectl logs POD --all-containers
- kubectl logs POD [-c CONTAINER] --follow
- kubectl logs POD [-c CONTAINER] --previous
---

## Debugging
-------------
### More logs
```bash
sq ski debug logs --help
```
---

## Debugging
-------------
### Attaching a shell
- kubectl exec -it deployment/ski-exemplar [-c CONTAINER] -- /bin/bash
---

## Debugging
-------------
### Attaching a shell
```bash
sq ski debug exec --help
```
---

## Debugging
-------------
### Rolling back
- "Rolling back" means (re)declaring a previous configuration
- Kubernetes doesn't have a rich "history" semantic
- kubectl rollout undo deployment/abc

---

## Debugging
-------------
### Using k9s
- [https://k9scli.io/](https://k9scli.io/)
---

## Workshop
-------------
### Prerequisites
- cd ~/Development/topsoil
- ./compost backend OR babushka shared-kube && sq update

### Troubleshooting exercise
- sq ski context switch -a ski-exemplar -r west -e staging
- sq ski context switch -a ski-paragon -r west -e staging
- sq ski context switch -a rubyexemplar -r west -e staging
