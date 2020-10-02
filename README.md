# code-server-k3s

Steps to build code-server image and deploy on an arm64 based k3s cluster

These steps do not apply to Code Server Enterprise. They describe how to build an ARM64 code-server (https://github.com/cdr/code-server) docker image and deploy it to a local kubernetes cluster (a three-node Raspberry Pi 4B running Rancher k3s in HA mode). 

Node/OS/Cluster configuration:
* Node OS: ubuntu 18.04 LTS (aarch64)
* NFS: for PV support, used Synology array in same LAN exporting NSF (Synology DSM v6.2.3)
* etcd: v 3.4.1
  * stand-alone etcd installed before k3s on cluster nodes to make it k3s ~HA)
* k3s:  GitVersion: v1.18.8+k3s1
*  
Build details for the pi cluster are here: [b8kerybuildlink]()
