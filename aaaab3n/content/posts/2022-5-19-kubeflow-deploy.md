---
layout: post
title: "Install Kubeflow on Ubuntu"
date: 2022-05-19  10:03:59 +0800
category: Technology
tags: 
    - Ubuntu
    - Kubeflow 
---

## Install
```bash
sudo snap install microk8s --classic --channel=1.21/stable
sudo snap install juju --classic
microk8s status --wait-ready
sudo usermod -a -G microk8s <username>
sudo chown -f -R <username> ~/.kube
# microk8s is running
# high-availability: no
#   datastore master nodes: 127.0.0.1:19001
#   datastore standby nodes: none
alias kubectl='microk8s.kubectl'
```
microk8s enable dns:10.19.127.36 storage
```bash
microk8s enable dns storage
microk8s config > ~/.kube/config
juju add-k8s myk8s
# This operation can be applied to both a copy on this client and to the one on a controller.
# No current controller was detected and there are no registered controllers on this client: either bootstrap one or register one.

# k8s substrate "microk8s/localhost" added as cloud "myk8s".
# You can now bootstrap to this cloud by running 'juju bootstrap myk8s'.
```
Deploy controller ( This step cost me nearly an hour) 
```bash
juju bootstrap myk8s my-controller
# Creating Juju controller "my-controller" on myk8s/localhost
# Bootstrap to Kubernetes cluster identified as microk8s/localhost
# Fetching Juju Dashboard 0.8.1
# Creating k8s resources for controller "controller-my-controller"
# Downloading images
# Starting controller pod
# Bootstrap agent now started
# Contacting Juju controller at 10.152.183.161 to verify accessibility...





# Now you can run
#         juju add-model <model-name>
# to create a new model to deploy k8s workloads.
```

We can check by running the following command:
```bash
🕙 14:39:42 ❯ juju controllers
Use --refresh option with this command to see the latest information.

Controller      Model       User   Access     Cloud/Region     Models  Nodes  HA  Version
my-controller*  controller  admin  superuser  myk8s/localhost       1      1   -  2.9.29
```

Add kubeflow
```bash
🕙 14:48:23 ❯ juju add-model kubeflow
Added 'kubeflow' model on myk8s/localhost with credential 'myk8s' for user 'admin'

 in ~ 🐍 v2.7.18
🕙 14:49:45 ❯ juju models
Controller: my-controller

Model       Cloud/Region     Type        Status     Access  Last connection
controller  myk8s/localhost  kubernetes  available  admin   just now
kubeflow*   myk8s/localhost  kubernetes  available  admin   never connected

node1% kubectl get namespaces
NAME                       STATUS   AGE
kube-system                Active   16h
kube-public                Active   16h
kube-node-lease            Active   16h
default                    Active   16h
controller-my-controller   Active   32m
kubeflow                   Active   70s
```

Deploy kubeflow
```bash
node1% juju deploy kubeflow-lite
```
When I am doing this, I get output  

```bash
ERROR resolving with preferred channel: Post "https://api.charmhub.io/v2/charms/refresh": dial tcp: lookup api.charmhub.io on 10.152.183.10:53: server misbehaving
```

And

```bash
ERROR resolving with preferred channel: Post "https://api.charmhub.io/v2/charms/refresh": dial tcp 185.125.188.57:443: i/o timeout
```

`LookUp` problem can be solved by reset microk8s dns. The latter was solved by turn off my [v2raya](https://v2raya.org/) transparent proxy.

Finally, I got

```bash
........
- set annotations for oidc-gatekeeper
  setting annotations:
    bundleURL: "ch:amd64/kubeflow-lite-60"
- upload charm seldon-core from charm-hub from channel latest/stable with architecture=amd64
- deploy application seldon-controller-manager from charm-hub with 1 unit with latest/stable using seldon-core
  added resource oci-image
- set annotations for seldon-controller-manager
  setting annotations:
    bundleURL: "ch:amd64/kubeflow-lite-60"
- upload charm training-operator from charm-hub from channel latest/stable with architecture=amd64
- deploy application training-operator from charm-hub with 1 unit with latest/stable
  added resource training-operator-image
- set annotations for training-operator
  setting annotations:
    bundleURL: "ch:amd64/kubeflow-lite-60"
- add relation argo-controller - minio
- add relation dex-auth:oidc-client - oidc-gatekeeper:oidc-client
- add relation istio-pilot:ingress - dex-auth:ingress
- add relation istio-pilot:ingress - jupyter-ui:ingress
- add relation istio-pilot:ingress - kfp-ui:ingress
- add relation istio-pilot:ingress - kubeflow-dashboard:ingress
- add relation istio-pilot:ingress - kubeflow-volumes:ingress
- add relation istio-pilot:ingress - oidc-gatekeeper:ingress
- add relation istio-pilot:ingress-auth - oidc-gatekeeper:ingress-auth
- add relation istio-pilot:istio-pilot - istio-ingressgateway:istio-pilot
- add relation kfp-api - kfp-db
- add relation kfp-api:kfp-api - kfp-persistence:kfp-api
- add relation kfp-api:kfp-api - kfp-ui:kfp-api
- add relation kfp-api:kfp-viz - kfp-viz:kfp-viz
- add relation kfp-api:object-storage - minio:object-storage
- add relation kfp-profile-controller:object-storage - minio:object-storage
- add relation kfp-ui:object-storage - minio:object-storage
- add relation kubeflow-profiles - kubeflow-dashboard
- add relation mlmd:grpc - envoy:grpc
Deploy of bundle completed.
```

![](https://snz04pap002files.storage.live.com/y4mlD09g9-IeOc_Zs9PqDFDu9CxL_oVx9wh9g_A3Z1n429RaJjACXh1YmgsJXFNbHd0ueJQ5iaAjTeSKYYVJk_VXNx44G8zLFJH3q39NxTsjtItfSbhv8hWLQVlVrPB50F3ZiOo-2Q1tRkqdrTUfOUPGK82mkL7-OTbnua4LKGWnETyOQwLkrUTU4lNcpvNGy-r?width=1024&height=537&cropmode=none)

## Reference
- [How to install Kubeflow v1.4](https://charmed-kubeflow.io/docs/install)