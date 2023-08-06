---
title: "K8s Basics"
slug: "K8s Basics"
description: "K8s Basics"
date: 2023-02-19T20:52:18+01:00
draft: true
editURL: https://github.com/Kmelow/dotslashrun/issues
tags: ["AWS", "Go", "EDA"]
---


> It maintains a stable set of replica Pods running at any given time

It contains the number of `replicas` and a Pod's `template` definition.

Usualy, `Deployments` are used instead of `ReplicaSet`s.

Example of `ReplicaSet`:

```yaml
apiVersion: apps/v1
kind: ReplicaSet
metadata:
  name: frontend
  labels:
    app: guestbook
    tier: frontend
spec:

  # It contains the number of replicas
  replicas: 3

    # Specify wich Pods are replicated
    selector:
    # Labels to seach for in Pods `metadata`
    matchLabels:
      tier: frontend

  # The template specifies a Pod template and spec
  template:
    metadata:
      labels:
        tier: frontend
    spec:
      containers:
      - name: php-redis
        image: gcr.io/google_samples/gb-frontend:v3
```

To get the number of replicas `k get rs`

## Scaling Replicas

In order to scale out or in (change the number of replicas) the options are

- `k scale --replicas=6 -f replica.yaml`: Using CLI and manifest file
- `k scale --replicas=6 replicaset <metadata.name>`: Using CLI and `ReplicaSet` meta name
- `k replace -f replica.yaml`: Update manifest file with `replica: 6`, then update with CLIto set new replica count



## Context

## References