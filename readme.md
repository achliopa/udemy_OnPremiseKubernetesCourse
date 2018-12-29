# Udemy Course - Learn DevOps: On-Prem or Cloud Agnostic Kubernetes

* [Course](https://www.udemy.com/learn-devops-on-prem-or-cloud-agnostic-kubernetes/)
* [Repository](https://github.com/wardviaene/on-prem-or-cloud-agnostic-kubernetes/)

## Section 1 - Introduction

### Lecture 1 - Introduction

* Course Overview
	* *Kubernetes Topic => Technology*
	* Install kubernetes on-prem => kubeadm
	* File, Block and Object Storage => Kubernetes Operators, Rook and ceph
	* Managing SSL (HTTPS apps & endpoints) => cert-manager
	* LDAP authentication => Dex with LDAP
	* Service Mesh, Load balancing and proxying => Envoy, Istio
	* Networking => Calico
	* Secret Store => Vault
	* PaaS => OpenShift Origin
* Course Objectives
	* be able to use K8s on-prem in a Cloud Agnostic way
	* use K8s in an enterprise env
	* be able to deploy k8s anywhere (our own integrations, storage, certificates, authentication and on)

### Section 2 - Introduction to Kubernetes

### Lecture 3 - High Level Introduction, CNCF's Trail map

* The [TrailMap Image](https://raw.githubusercontent.com/cncf/trailmap/master/CNCF_TrailMap_latest.png)
* shows how technologies fit together in the ecosystem
* 1st step: Containerization (Docker Containers)
* 2nd Step: CI/CD(Jenkins,Travis,Spinnaker)
* 3rd Step: Orchestration (Kubernetes)
* 4th Step: Observability & Analysis (Fluentd,Prometheus)
* 5th Step: Service Mesh & Discovery (Istio,Envoy,Linkerd,CoreDNS)
* 6th Step: Networking (CNI,Flanner,Calico)
* 7th Step: Distributed DB ()
* 8th Step: Messaging (rabbitMQ)

## Section 3 - Installing Kubernetes

### Lecture 4 - Introduction to kubeadm

* Whole section same as Section 8 from Learn Devops: The complete Kubernetes Course. going to skip this.refer to that course's notes kubeadm already on our vagrant vm

## Section 4 - Operators

### Lecture 7 - Introduction to Operators

* An Operator is a method of packaging, deploying and managing a K8s Application
* It puts operational knowledge in an Application
* It brings the user closer to the experience of managed cloud services rather than having to know all the specifics aof an app deployed in Kubernetes
* Once an Operator is deployed, it can be managed using CRDs
* It also provides a great way to deploy Stateful services on K8s (because a lot of complexities are hidden from end-user)
* CRDs are extensions of the K8s API
* It allows the K8s user to use custom objects (the kind of obj we use in YAML files) and mod/create/delete them on cluster
* We can run a kubectl create on a CRD yaml to spin up a db on cluster
* CRDs are not available in all clusters. they can be dynamically reg/dereg
* Operators include CRDs
* etcd, Rook, Prometheus, Vault are examples of technologies that can be deployed as an Operator
* e.g etcd (distributed key-value store)
	* once etcd operator is deployed, a new etcd cluster can be created by using the follwoing yaml file
	```
	apiVersion: "etcd.database.coreos.com/v1beta2"
	kind: "EtcdCluster"
	metadata:
	  name: "example-etcd-cluster"
	spec:
	  size: 3
	  version: "3.2.13"
	```
	* resizing the cluster is a matter of moding the yaml file (+ kubectl apply). same holds for version
* Using Operators simplifies deployment and management
* A lot of Sw is released using Operators in K8s (e.g Prostgres)
* We can build our own operators using the following tools.
	* The Operator SDK: makes it easy to build an operator, rather than having to learn the Kubernetes API specifis
	* Operator Lifecycle Manager: oversees installation, updates and management of the lifecycle of all the operators
	* Operator metering: Usage reporting
* In this course Operators will be used for deployments

## Section 5 - Rook

### Lecture 8 - Introduction to Rook

* 