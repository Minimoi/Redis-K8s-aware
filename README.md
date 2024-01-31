# Redis + Sentinel K8s-aware

## Description

This repo contains the deployment conf of a HA Redis with Sentinel.

## Kubernetes integration

In order to avoid deploying an additional HAProxy to handle connections from clients not supporting Sentinel, we leveraged native Kubernetes and Sentinel mechanisms.

### Kubernetes Service

We add a label `redis-role` on the pods and a Kubernetes Service that always point to the pod with the `redis-role: master` label. That allows us to use the native Kubernetes routing and load-balancing and avoid an additional stack.

### Sentinel failover

We use the `sentinel client-reconfig-script` command, that triggers during each failover after promoting a slave to master, to run a custom script. The script uses `kubectl` to update the label of its own pod.

### kubectl

`kubectl` is loaded from an init container in order to avoid building a custom image and to decorrelate the Redis & kubectl versions.
