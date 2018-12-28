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