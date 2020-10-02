# code-server-k3s

Steps to build code-server image and deploy on an arm64 based k3s cluster

These steps do not apply to Code Server Enterprise. They describe how to build an ARM64 code-server docker (https://github.com/cdr/code-server) image and deploy it to a local kubernetes cluster (a three-node Raspberry Pi 4B running Rancher k3s in HA mode).

Node/OS/Cluster configuration:
* Node OS: ubuntu 18.04 LTS (aarch64)
* NFS: for PV support, used Synology array in same LAN exporting NSF (Synology DSM v6.2.3)
* etcd: v 3.4.1
  * stand-alone etcd installed before k3s on cluster nodes to make it k3s ~HA)
* k3s:  GitVersion: v1.18.8+k3s1
  * disabled default ingress (traefik), to use nginx + metallb (since is a baremetal/on-prem cluster)
* Build details for the pi cluster are here: [b8kery build notes](https://github.com/kcalmond/b8kery)

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
* **Create the PV**: [coder-pv.yaml](https://github.com/kcalmond/code-server-k3s/blob/main/coder-pv.yaml)
* **Create the PVC**: [coder-pvc.yaml](https://github.com/kcalmond/code-server-k3s/blob/main/coder-pvc.yaml)
```
coder@hackberry:~/github/cdr/b8kery> k create -f ./coder-pv.yaml
persistentvolume/coder-pv created

coder@hackberry:~/github/cdr/b8kery> k create -f ./coder-pvc.yaml
persistentvolumeclaim/coder-pv created

coder@hackberry:~/github/cdr/b8kery> k get pv,pvc
NAME                        CAPACITY   ACCESS MODES   RECLAIM POLICY   STATUS   CLAIM            STORAGECLASS   REASON   AGE
persistentvolume/coder-pv   10Gi       RWX            Retain           Bound    coder/coder-pv   manualnfs               3h10m

NAME                             STATUS   VOLUME     CAPACITY   ACCESS MODES   STORAGECLASS   AGE
persistentvolumeclaim/coder-pv   Bound    coder-pv   10Gi       RWX            manualnfs      3h10m
```

* **Create the deployment**: [coder-deploy.yaml](https://github.com/kcalmond/code-server-k3s/blob/main/coder-deploy.yaml)
```
coder@hackberry:~/github/cdr/b8kery> k create -f ./coder-deploy.yaml
deployment.apps/code-server created

coder@hackberry:~/github/cdr/b8kery> k get pods
NAME                           READY   STATUS    RESTARTS   AGE
code-server-6ddbd884c6-zhmcx   1/1     Running   0          4s

coder@hackberry:~/github/cdr/b8kery> k describe deployment -n coder code-server
Name:               code-server
Namespace:          coder
CreationTimestamp:  Fri, 02 Oct 2020 01:12:19 +0000
Labels:             app=code-server
Annotations:        deployment.kubernetes.io/revision: 1
                    field.cattle.io/publicEndpoints:
                      [{"addresses":["192.168.200.4"],"port":80,"protocol":"TCP","serviceName":"coder:code-server","allNodes":false}]
Selector:           app=code-server
Replicas:           1 desired | 1 updated | 1 total | 1 available | 0 unavailable
StrategyType:       Recreate
MinReadySeconds:    0
Pod Template:
  Labels:  app=code-server
  Containers:
   code-server:
    Image:        kcalmond/code-server-arm64:3.5.0
    Port:         8080/TCP
    Host Port:    0/TCP
    Environment:  <none>
    Mounts:
      /home/coder from coder-pv (rw)
  Volumes:
   coder-pv:
    Type:       PersistentVolumeClaim (a reference to a PersistentVolumeClaim in the same namespace)
    ClaimName:  coder-pv
    ReadOnly:   false
Conditions:
  Type           Status  Reason
  ----           ------  ------
  Available      True    MinimumReplicasAvailable
  Progressing    True    NewReplicaSetAvailable
OldReplicaSets:  <none>
NewReplicaSet:   code-server-6ddbd884c6 (1/1 replicas created)
Events:          <none>
```

* **And create the load balancer**: [coder-lb.yaml](https://github.com/kcalmond/code-server-k3s/blob/main/coder-lb.yaml)
```
coder@hackberry:~/github/cdr/b8kery> k create -f ./coder-lb.yaml
service/code-server created

coder@hackberry:~/github/cdr/b8kery> k get svc -n coder
NAME          TYPE           CLUSTER-IP     EXTERNAL-IP     PORT(S)        AGE
code-server   LoadBalancer   10.43.216.33   192.168.200.4   80:30485/TCP   3h18m
```

If everything is working then you should be able to load code-server see the default vscode explorer UI:

<img src="https://github.com/kcalmond/code-server-k3s/blob/main/Welcome-coder-Code-OSS.jpg" align="center" width="700">
