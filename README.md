
# Deploying ScyllaDB on Kubernetes using StatefulSet

This guide provides instructions and a sample manifest for deploying ScyllaDB, a highly available NoSQL database, on Kubernetes using StatefulSet.

## Prerequisites
- Access to a Kubernetes cluster 
- kubectl command-line tool installed and configured to access your cluster
- Persistent StorageClass configured in your Kubernetes cluster

#### Asynchronous non-blocking I/O (AIO) requests
When setting up a ScyllaDB cluster, you should make sure the aio-max-nr value is set to 1048576 or higher. This parameter limits the number of Asynchronous non-blocking I/O (AIO) requests that the Linux Kernel can handle at once. It helps ScyllaDB perform well in environments with heavy I/O workloads.

To make sure that aio-max-nr value is set to 1048576 or higher, follow the below steps:


Allows you to access the VM's shell.
```bash
  minikube ssh
```
This command reads the current value of the aio-max-nr parameter from the /proc file system. This parameter controls the maximum number of outstanding asynchronous I/O operations system-wide (by deafult value 65536) 

```bash
  cat /proc/sys/fs/aio-max-nr  
```
Here, you're echoing the text fs.aio-max-nr = 1048576 into the file /etc/sysctl.conf. This file is used to set kernel parameters persistently across system reboots in Linux.
```bash
  echo "fs.aio-max-nr = 1048576" | sudo tee -a /etc/sysctl.conf 
```
This command reloads the kernel parameters from the specified configuration file into the running system.
```bash
  sudo sysctl -p /etc/sysctl.conf 
```
This command reads and displays the value of aio-max-nr.
```bash
  cat /proc/sys/fs/aio-max-nr 
```


## Deployment

### Step 1: Deploying ScyllaDB

Apply the Storageclass manifest to your Kubernetes cluster:

```bash
  kubectl apply -f storageclass.yaml
```
Apply the PersistentVolume manifest to your Kubernetes cluster:
```bash
  kubectl apply -f pv.yaml
```
Apply the configmap manifest to your Kubernetes cluster:
```bash
  kubectl apply -f configmap.yaml
```
Apply the statefulset manifest to your Kubernetes cluster:
```bash
  kubectl apply -f statefulset.yaml
```
Apply the service manifest to your Kubernetes cluster:
```bash
  kubectl apply -f service.yaml
```

Before acccessing scylladb pods make sure that the pods are in running state.
### Step 2: Accessing ScyllaDB
Once deployed, you can enter into the container by using the below command:
```bash
  kubectl exec -it scylla-0 -- bash
```
Once you are into the container you can check the rest of the pod state by using nodetool. It will show the status (UN) or (DN).
 ```bash
  nodetool status
```
Now you can connect to the pod using CQL tool.
 ```bash
  cqlsh ${POD_IP} 9042
```

This README provides a basic guide to deploying and managing ScyllaDB on Kubernetes. Customize the steps and manifest as per your specific deployment requirements and best practices.



