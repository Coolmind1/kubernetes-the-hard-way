# Cloud Infrastructure Provisioning

Kubernetes can be installed just about anywhere physical or virtual machines can be run. In this lab we are going to focus on Google Cloud Platform (IaaS).

This lab will walk you through provisioning the compute instances required for running a H/A Kubernetes cluster. A total of 9 virtual machines will be created.

After completing this guide you should have the following compute instances:

```
gcloud compute instances list
```

````
NAME         ZONE           MACHINE_TYPE   INTERNAL_IP  STATUS
controller0  us-central1-f  n1-standard-1  10.240.0.20  RUNNING
controller1  us-central1-f  n1-standard-1  10.240.0.21  RUNNING
controller2  us-central1-f  n1-standard-1  10.240.0.22  RUNNING
etcd0        us-central1-f  n1-standard-1  10.240.0.10  RUNNING
etcd1        us-central1-f  n1-standard-1  10.240.0.11  RUNNING
etcd2        us-central1-f  n1-standard-1  10.240.0.12  RUNNING
worker0      us-central1-f  n1-standard-1  10.240.0.30  RUNNING
worker1      us-central1-f  n1-standard-1  10.240.0.31  RUNNING
worker2      us-central1-f  n1-standard-1  10.240.0.32  RUNNING
````

> All machines will be provisioned with fixed private IP addresses to simplify the bootstrap process.

To make our Kubernetes control plane remotely accessable a public IP address will be provisioned and assigned to a Load Balancer that will sit in front of the 3 Kubernetes controllers.

## Create the Kubernetes Public IP Address

Create a public IP address that will be used by remote clients to connect to the Kubernetes control plane:

```
gcloud compute addresses create kubernetes
```

```
gcloud compute addresses list
```
```
NAME        REGION       ADDRESS         STATUS
kubernetes  us-central1  146.148.34.151  RESERVED
```

## Provision Virtual Machines

All the VMs in this lab will be provisioned using Ubuntu 16.04 mainly because it runs a newish Linux Kernel that has good support for Docker.

```
$ uname -a
```

```
Linux controller0 4.4.0-28-generic #47-Ubuntu SMP Fri Jun 24 10:09:13 UTC 2016 x86_64 x86_64 x86_64 GNU/Linux
```

### etcd

```
gcloud compute instances create etcd0 \
 --boot-disk-size 200GB \
 --can-ip-forward \
 --image-project ubuntu-os-cloud \
 --image ubuntu-1604-xenial-v20160627 \
 --machine-type n1-standard-1 \
 --private-network-ip 10.240.0.10
```

```
gcloud compute instances create etcd1 \
 --boot-disk-size 200GB \
 --can-ip-forward \
 --image-project ubuntu-os-cloud \
 --image ubuntu-1604-xenial-v20160627 \
 --machine-type n1-standard-1 \
 --private-network-ip 10.240.0.11
```

```
gcloud compute instances create etcd2 \
 --boot-disk-size 200GB \
 --can-ip-forward \
 --image-project ubuntu-os-cloud \
 --image ubuntu-1604-xenial-v20160627 \
 --machine-type n1-standard-1 \
 --private-network-ip 10.240.0.12
```

### Kubernetes Controllers

```
gcloud compute instances create controller0 \
 --boot-disk-size 200GB \
 --can-ip-forward \
 --image-project ubuntu-os-cloud \
 --image ubuntu-1604-xenial-v20160627 \
 --machine-type n1-standard-1 \
 --private-network-ip 10.240.0.20
```

```
gcloud compute instances create controller1 \
 --boot-disk-size 200GB \
 --can-ip-forward \
 --image-project ubuntu-os-cloud \
 --image ubuntu-1604-xenial-v20160627 \
 --machine-type n1-standard-1 \
 --private-network-ip 10.240.0.21
```

```
gcloud compute instances create controller2 \
 --boot-disk-size 200GB \
 --can-ip-forward \
 --image-project ubuntu-os-cloud \
 --image ubuntu-1604-xenial-v20160627 \
 --machine-type n1-standard-1 \
 --private-network-ip 10.240.0.22
```

### Kubernetes Workers

```
gcloud compute instances create worker0 \
 --boot-disk-size 200GB \
 --can-ip-forward \
 --image-project ubuntu-os-cloud \
 --image ubuntu-1604-xenial-v20160627 \
 --machine-type n1-standard-1 \
 --private-network-ip 10.240.0.30
```

```
gcloud compute instances create worker1 \
 --boot-disk-size 200GB \
 --can-ip-forward \
 --image-project ubuntu-os-cloud \
 --image ubuntu-1604-xenial-v20160627 \
 --machine-type n1-standard-1 \
 --private-network-ip 10.240.0.31
```

```
gcloud compute instances create worker2 \
 --boot-disk-size 200GB \
 --can-ip-forward \
 --image-project ubuntu-os-cloud \
 --image ubuntu-1604-xenial-v20160627 \
 --machine-type n1-standard-1 \
 --private-network-ip 10.240.0.32
```