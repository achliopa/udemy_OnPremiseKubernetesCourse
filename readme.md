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

* Rook is an opensource orchestrator for distributed storage systems running in Kubernetes 
* Rook allows us to use storage systems on Kubernetes clusters (that cannot use public cloud storage or want to be cloud agnostic)
* If we are on the public cloud its very easy to attach a storage volume to a pod, to allow our app to persist its data even when the pod or node shuts down
* Its not that easy when we are not on major cloud providers (AWS, Google, Azure)
* Rook wants to make it easy for us to use a storage ssytem even when we are not on one of the major cloud providers or when using on-prem cluster
* Rook automates the confgiguration, deployment, maintenance of distributed storage software
* This way we dont have to worry about the difficulties of setting up storage systems. Rook will orchestrate all this management for us
* Rook is new. it looks promising and will become stable
* It uses Ceph as underlying storage. Minio and CockroachDB are also available. More storage engines will be added in the future
* the [architecture of Rook](https://rook.io/docs/rook/v0.7/media/rook-architecture.png)
	* kubectl: used to create new objects (Storage Clusters, Storage Pools, Object Store, File Store)
	* kube-apiserver
	* distributed etcd DB: stores configuration
	* Rook Operator: used to start a Rook CLuster
	* Rook Agent: heart of rook cluster. has ceph ot NFS.  uses a fkexvolume driver to mount and attach storage 
	* kubelet w/ Rook Volume plugin: in storage node talks with flexvolume driver

### Lecture 9 - Ceph

* Ceph is software that provides object, file, and block storage
* its open source
* its distributed without a single point of failure
* Ceph replicates data to make it fault tolerant (a node can fail, but we still have the data available)
* self healing ans self managing
* scalable to exabyte level (WTF????)
* Ceph provides 3 different types of storage:
	* File Storage: to store files and directories, similar to accessing files over NFS or using a NAS or EFS (Elastic File System on AWS)
	* Block Storage: like a hard drive, to store data using a filesystem. A database needs block storage. Examples are SAN (Storage Area Network, which can provide block storage to Servers) or EBS (Elastic Block Storage on AWS). Typical use case is to store files for OS, DB storage etc.
	* Object Storage: To store any type of data as an object, identified by a key, with the possibility to add metadata. This type of storage lends itself to be distributed and scalable. For example AWS S3 provides Object Storage. Can be used to store unstructured data like pictures, website assets, videos, log files etc...
* Ceph Components (it has multiple components):
	* Ceph Monitor (minimum 3): maintains a map of the cluster state for the other ceph components (the daemons) to communicate. Also responsible for authentication between daemons and clients
	* Ceph Manager Daemon: responsible to keep track of runtime metrics and the cluster state
	* Ceph OSDs (Object Storage Daemon, minimum 3): stores the data, is responsible fro replication, recovery, rebalancing and provides information for the monitoring daemons
	* MDSs (Ceph Metadata Server): stores metadata for the Ceph FileSystem storage type (not for block/object storage types)
* Ceph stores data as objects within logical storage pools
* It uses the CRUSH algorithm (Controlled Replication Under Scalable Hashing) which allows Ceph to be scalable
* Object storage in Ceph is provided by the distributed object sotrage mechanism within Ceph
* Ceph software libraries (librados) provides clients access to the "Reliable Autonomic Distributed Object Store" (RADOS)
* RADOS provides a reliable, autonomous, distributed object store comprised of self-healing, self-managing, intelligent storage nodes
* There is also a RESTful interface that can provide an AWS S3 compatible interface to this object store
* Ceph block storage is provided by Ceph's RADOS Block Device (RBD)
* RBD is built on top of the same Ceph Object Storage. Ceph stores the block images as objects in the object store
* Its also built on librados, the SW library
* Architecture: 
	* Data comes from Ceph's file storage, block storage or object storage and Cephwill store this data as an object in Ceph's RADOS (the object store)
	* CEPH's RADOS contains OSDs w/ data and Monitors
	* Each object is stored in the Object Storage Device (OSD)
	* OSDs will run on multiple nodes and they will handle the read/write operations to theis underlying sotrage

### Lecture 10 - Ceph with Rook

* Rook Pods contained in a Node (bottom up):
	* OSDs (with stored data)
	* Ceph daemons (Monitor POD, Manager POD, MDS pod, RGw pod)
	* Rook pods (multiple Agents, one Operator)
* Apps use Rook through Volume Claim
* Rook also supports 3 types of storage: block, file and object
* Ceph can be used for all of them (other backends are also possible)
* Rook does anice job in hidding it from us. most of the configuration is hidden
* We will use k8s YAML files to set configuration options (rook does that)
* First, we need to deploy the rook operator
	* using the provided YAML files
	* using the helm chart
* Then we can create the rook cluster
	* Also using yaml definitions (this time using: apiVersion: rook.io/v1alpha1)
	* This will use the rook operator rather than the Kubernetes API
* After that, block / file / object storage can be configured
	* Using the rook API and K8s storage API - using this storage API means using rook storage will become as easy as using for example AWS EBS on NFS

### Lecture 11 - Demo: Rook with Ceph (Part I)

* we log in to our master node in digital ocean
* we will add 2 more nodes in digital ocean for this section (we follow th kubeadm way)
* if i want to have the join command to add nodes i can use `sudo kubeadm token create --print-join-command` and get the command to add a node
* in master node i git clone https://github.com/wardviaene/on-prem-or-cloud-agnostic-kubernetes/
* i go to on-prem-or-cloud-agnostic-kubernetes/rook
* the re i find a README with instructions and yaml files
* i will install rook with
```
kubectl create -f rook-operator.yaml
kubectl create -f rook-cluster.yaml
```
* i vim rook-operator.yaml. it has a namespace a clusterole, a serviceaccount, a clusterolebinding, a deployment of rook operator + env variables.
* i apply it and check the pods status in namespace rook-system. operator runs on node-01 and 3 agents run as daemons in each node
* i vim rook-cluster.yaml. it uses a Cluster CRD with backend ceph. we spec a path for the data (on the node that runs rook). 
* i apply it and check for pods in rook namespace. i have 1 rook-ceph-monitor pod in each node.
* then i vim rook-storageclass.yml. it is a Pool CRD (datapool) replicated 2 times and a StorageClass from vanila k8s with pool as provision. this is the place k8s connect to Rook
* i apply it and check the pods in rook ns. 3 osds 1 manager and one api pods are added distributed among the nodes
* i vim rook-tools.yaml.  a pod  running the rook/toolbox image. these tools can be used to check status of cluster. i apply it
* i `kubectl exec -it rook-tools -n rook -- bash` to log in tools pod. i run `rookctl status` it adds the storage of nodes
* i vim mysql-demo.yml. a service with a persistentvolumeclaim on rook-block storageclass and a deployment
* i apply it and see kubectl get pv (i see 3 volumes one per node only one is bound the others are redundant)

### Lecture 12 - Demo: Rook with Ceph (Part II)

* we will put some data in mysqlDB that i will survive a pod crash
* i log in mysql pod `kubectl exec -it demo-mysql-7fbfff59d9-9s475 -- bash`
* i start mysql client `mysql -u root -pchangeme`
* i create some data `create database demo; use demo; create table helloworld(id int, hello varchar(100)); insert into helloworld values (1, 'world');`
* i confirm insertion `select * from helloworld;` and exit `\q`
* i will check where this pod is running on with -o wide. it runs on node 2
* i want to make sure that if the pod gets killed it will not be scheduled to node-2 `kubectl cordon kubernetes-node-02`. if get pods i see that scheduling is disabled on node-02. a real test scenario is to take down the node completely to see what happns
* i delete the pod ` kubectl delete pod demo-mysql-7fbfff59d9-9s475` a new one is created in node-03
* i log in and go to mysql to check the query. SUCCESS
* i can completely shutdown node-02 `shutdown -h now` i retest and all is ok
* true HA is achieved if in app level there are checks for fast switchover (MYsql master - slave) as switchover in k8s is not instant. 
* if i go in tools pod and check status i see the node lost (OSD)

### Lecture 13 - Demo: Rook with Object Storage

* we ll see how to enable object sotrage in rook
* we switch on the node-02 from digital ocean and uncordone it from master `kubectl uncordon kubernetes-node-02`
* i vim rook-storageclass-objectstore.yaml. it sets a ObjectStore CRD using an s3 gateway (s3 compatible) so that our app can use an s3 client
* i get pods in namespace rook and see the  new pod running rook-ceph-rgw-my-store which comes with a service. this service is the s3 gateway to be used in the k8s cluster
* i log in the rook-tools pod `kubectl exec -it rook-tools -n rook -- bash` and create a new s3 user `radosgw-admin user create --uid rook-user --display-name "A rook rgw User" --rgw-realm=my-store --rgw-zonegroup=my-store`
* like in AWS s3 i have an access key and a secret key. i can configure env vars to use them to connect to the s3 object store
* in rook-tools pod bash console i expose them as env vars
```
export AWS_HOST=rook-ceph-rgw-my-store.rook #service hostname
export AWS_ENDPOINT=10.97.30.45 # service ipaddress
AWS_ACCESS_KEY=ER748GC9EYLA13YMMLZ2 # key appears in user yaml olog after creation
AWS_SECRET_ACCESS_KEY=ck0LyAJ7RPpdtwCpIZErJiv17YVYCft3rZ2NPACO # same as above
```
* we can use s3cmd (in rook-tools) `s3cmd --help`
* i use it to create an s3 bucket `s3cmd mb --no-ssl --host=${AWS_HOST} --host-bucket= s3://demobucket`
* to test it i create a file `echo 'hello world' > test` `cat test`
* i will put this file in s3 `s3cmd put test --no-ssl --host=${AWS_HOST} --host-bucket= s3://demobucket`
* if i ls the bucket `s3cmd ls --no-ssl --host=${AWS_HOST} --host-bucket= s3://demobucket` is see the test file

### Lecture 14 - Demo: Rook with File Storage

* i vim rook-storageclass-fs.yaml in manster node. i have a FileSystem CRD replicated and named myfs using a metadata server. i apply it . 2 pods are added to rook ns
* i vim fs-demo.yaml. an ubutnu pod using myfs as nfs in /data path
* i apply it and log in the ubuntu pod `kubectl exec -it ubuntu -- bash` and type `mount |grep data`. i see the 3 replicas mount on /data.
* i can use /data as anormal fs
* in anycase object dtorage is preferable

## Section 6 - Manage TLS Certificates

### Lecture 15 - Introduction to cert-manager

* if we want to use a secure http connection (https) we need to have certificates
* those certificates can be bought, or can be issued by some public cloud providers, like AWS's Certificate Manager
* Managing SSL/TLS certificates ourselves often takes a lot of time and are time consuming to install and extend
* We cannot issue our own certificates for production websites. they are not trusted by the common internet browsers 
* Cert-manager can ease the issuing of certificates and the management of it
* Cert-manager can use letsencrypt
* Letsencrypt is a free, automated ans open Certificate Authority
* Lets encrypt can issue certificates for free for our app or website
* we need to prove to lets encrypt that we are the owner of a domain and they will issue a certificate for us
* the cerificate is recognized by major sw vendors and browsers
* cert-manager can automate the verification process for lets encrypt
* with lets encrypt we have to renew certificates every couple of months
* cert-manager will periodically check the validity of the certificates and will start the renewal process if necessary
* Lets encrypt + cert manager takes away a lot of hassle to deal with certificates allowing us to secure our endpoints in an easy affordable way
* we can only issue certificates for a domain name we own
* Cert-manager architecture:
	* Issuers (object): letsencrypt-staging, letsencrypt-prod,vault-prod(coming soon)
	* cert-manager (communicates with issuers and issues certificates)
	* Certificates: foo.bar.com(ussuer: letsencrypt-staging) www.example.com, example.com(issuer: letsencrypt-prod)
	* Kubernetes-secrets: signed keypair (per certificate)
* staging is for dev, prod is for production

### Lecture 16 - Demo: cert-manager (Part I)

* we'll see how to use cert-manager to issue certificates from letsencrypt
* i am in master node.
* in on-prem-or-cloud-agnostic-kubernetes/helm we need to install helm first
* in README there are instruction. i will install helm client on master node `curl https://raw.githubusercontent.com/kubernetes/helm/master/scripts/get | bash`
* our cluster is rbac enabled (defautl) so we need to apply the rbac-config.yml (install tiller and give priviledges)
* we are ready to init helm `helm init --service-account tiller`
* we go to ../cert-manager and see the README for instruction
* we need first an ingress controller (without LB) we will use helm to install it
```
helm install --name my-ingress stable/nginx-ingress \
  --set controller.kind=DaemonSet \
  --set controller.service.type=NodePort \
  --set controller.hostNetwork=true
```
* this intall installs an ingress controller in each node. for big clusters we dont need DeamonSet. just a Deploym,ent with some instances
* we dont use LB (a LB agnostic cluster) so NodePort is OK. it will expose port 80 and 443 on each node through ingress controller
* to delete installation `helm del --purge my-ingress` and `helm del --purge cert-manager`
* i get pod to see the ingress controller(s)
* i `kubectl edit pod my-ingress-nginx-ingress-controller-nbvxw ` and see there is a hostPort on 80 and 443
* to access externally our cluster we need to add these ports to the firewall

### Lecture 17 - Demo: cert-manager (Part II)

* in DigitalOCean i mod my firewall adding HTTP and HTTPS fro all Ips
* skipping this step will cause the ssl ert to fail as letsencrypt uses these ports to comm with our ip address for verification
* we continue with README instr. we will create an app and add ingress rule (myapp.yml and myapp-ingress.yml) a demo app as a Deployment and an Ingress Rule for the app (at myapp.agileng.io on our domain)
* i need to add the ip to our dns provider. i will choose node-01 ip from digital ocean to add it to our DNS panel in namecheap. 
* the problem with digital ocean is that the ips are not static. if node is down they change. the solution is to enable floating IP address in the node-01. this floating ip will move from one node to the other if any one is down
* in namecheap i add an A record withhost (myapp) and Ip the ip of node-01
* another option is to put a loadbalancer in front of the cluster and use its ip address
* i hit myapp.agileng.io and it works
* i am finally ready to create the cer-manager in my cluster (!!!) . i use helm
```
helm install \
    --name cert-manager \
    --namespace kube-system \
    stable/cert-manager
```
* cert-manager is installed sucessfully. the log gives us directions on how to issue our certs
* 2 ready scripts are available. issuer-staging.yml and issuer-prod.yml
* ALWAYS start with staging...
```
apiVersion: certmanager.k8s.io/v1alpha1
kind: Issuer
metadata:
  name: myapp-letsncrypt-staging
  namespace: default
spec:
  acme:
    # The ACME server URL
    server: https://acme-staging-v02.api.letsencrypt.org/directory
    # Email address used for ACME registration
    email: your@email.inv
    # Name of a secret used to store the ACME account private key
    privateKeySecretRef:
      name: myapp-letsncrypt-staging
    # Enable HTTP01 validations
    http01: {}

```
* in both ymls we put our email address
* i apply staging and production issuer CRDs
* the i `vim certificate-staging,yml` that contains the Certificate CRD. the certificate will be stored in a secret. also put our domains credentials in yaml
```
apiVersion: certmanager.k8s.io/v1alpha1
kind: Certificate
metadata:
  name: myapp
  namespace: default
spec:
  secretName: myapp-tls-staging
  issuerRef:
    name: myapp-letsncrypt-staging
  commonName: myapp.agileng.io
  #dnsNames:
  #- www.myapp.newtech.academy
  acme:
    config:
    - http01:
        ingress: myapp
      domains:
      - myapp.agileng.io
      #- www.myapp.newtech.academy

```
* i apply it and check with `kubectl get certificate` and `kubectl describe certificate myapp` and see the complete history of the certificate. with `kubectl describe ingress` i see it was updated when cert was created (rule injection)
* staging certs are not recognized by browsers. are only for testing
* to configure the cert i config the Ingress rule `vim myapp-ingress.yml` enabling the tls section
* i check https://myapp.agileng.io (it works but marked not safe)
* i do the production cert. `vim certificate-prod.yml` adding our domain name.
* i apply it and get an error as a certiicate with same name exists. i delete the old one `kubectl delete -f certificate-staging.yml` the new cert is stored in secrets as myapp-tls-prod. we need to mod the myapp-ingress.yml to use the new secret
* we repally and test https://myapp.agileng.io. it works and its GREEN!! its valid for 3months though