# code-server-k3s

Steps to build code-server image and deploy on an arm64 based k3s cluster

These steps do not apply to Code Server Enterprise. They describe how to build an ARM64 code-server docker (https://github.com/cdr/code-server) image and deploy it to a local kubernetes cluster (a three-node Raspberry Pi 4B running Rancher k3s in HA mode).

Node/OS/Cluster configuration:
* Node OS: ubuntu 18.04 LTS (aarch64)
* NFS: for PV support, used Synology array in same LAN exporting NSF (Synology DSM v6.2.3)
* etcd: v 3.4.1
  * stand-alone etcd installed before k3s on cluster nodes to make it k3s ~HA)
* k3s:  GitVersion: v1.18.8+k3s1
* Build details for the pi cluster are here: [b8kerybuildlink]()

### Goals:
* Build arm64 code-server image from source
* Create k8s deployment manifests to run arm64 image on k3s/pi cluster

There are some pre-built docker images available (first two choices below). I went with the third option below (build and run) to learn more about the code-server project (this was my first exposure to it).

Pre-built docker images for code-server are available:
* notes on running codercom/code-server:latest - [CoderCom-run-Image.md](https://github.com/kcalmond/code-server-k3s/blob/main/CoderCom-run-Image.md)
* notes on running linuxserver/code-server - [LinuxServerIO-run-Image.md](https://github.com/kcalmond/code-server-k3s/blob/main/LinuxServerIO-run-Image.md)
* notes on **building and running** kcalmond/code-server-arm64:3.5.0 are here - [code-server-arm64-build-run-image.md](https://github.com/kcalmond/code-server-k3s/blob/main/code-server-arm64-build-run-image.md)

### Kubernetes manifests
Four yamls below accomplish the following:
* create the PV: [coder-pv.yaml]
* create the PVC
* create the deployment
* create the load balancer
