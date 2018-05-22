# Glossary of Terms Used in VC3 Documentation


## **Portal Users**

### Allocation
* An Allocation refers to an User and a Resource Each Allocation must be owned by an User. Allocations are divisible/fractionable, and can be given to Projects. Allocations may not be oversubscribed. But unbounded Allocations may be parents of multiple unbounded SubAllocations. Bounded Allocations cannot spawn unbounded SubAllocations. If a Resource grants hard allocation and allows backfill mode, those are two distinct Allocations (one hard and one unbounded)

### Authentication
* The current mechanism for users to sign-up and create accounts into the VC3 project is by authenticating themselves with their GlobusID account.

### MFA
* multi-factor authentication

### Project
* a collection of “Allocations”. It has at least 1 “user owner”, and 0 or more non-owner members. The owner is also a member.

### Request
* Entity that encapsulates all information that defines a particular virtual cluster. Creating a new Request triggers creation of the cluster.

### Request templates
* a list of pre-existing forms to be used as base for new cluster requests creation.

### Resource
* Any target on which a vc3-builder will run to provide computing power to a virtual cluster.

### Resource profiles
* a list of pre-existing forms to be used as base for new resource definition.

### Service unit
* Service units are essentially just walltime hours, with minimum charges based on minimum cores or minimum nodes per job. Much like HEPSPEC, the SUs can be normalized/converted based on LINPACK benchmarks. Doc from XSEDE: https://portal.xsede.org/knowledge-base/-/kb/document/bazo

* For storage, possibly with multiple allocations per user, examples are scratch disk vs long term storage.

* Exotic devices like GPUs may or may not be accounted for, depending on the resource.

### Sub-Allocation
* A SubAllocation can be defined in terms of fraction or units (cpuhours?, $dollars, HEPSPEC) or be unbounded. SubAllocations are children of an Allocation.

### User
* Every User has 0 or more Allocations. Users are owners or members of one or more projects. A User in a project can make Request(s) utilizing project member’s Allocations


## **Project Developers**

### cluster states
  List of each possible state of a cluster throughout its lifecycle:

* NEW - Request was just created.
* VALIDATED - Request has been validated for basic correctness.
* PENDING - Request is valid and is waiting to be instantiated.
* GROWING - Cluster is in the process of being instantiated but is not yet usable.
* RUNNING - Cluster is ready to use.
* SHRINKING - Cluster resources are being removed.
* TERMINATING - Cluster is about to be destroyed.
* TERMINATED - Cluster no longer exists.

### credible
* Credible is a 3rd-party utility for programmatically generating, storing, and retrieving security tokens.

### dynamic infrastructure
* Services that are instantiated upon a virtual cluster request, such as the factory.

### factory
* The scheduler and resource manager for middleware.

### formatter
* A plugin that augments the output of |Flake8| when passed to flake8 --format.

### info service
* Long-running daemon that interacts with the information database on behalf of other services.

* The VC3 info service serves as both a persistence mechanism for the overall service, and a message bus between components. Information is stored and retrieved in the form of JSON-formatted documents, which thus form a single tree of information entities/nodes. The service optionally allows access security by enforcing ACLs at each node level.

### PIN
* Personal Identification Number. One-time password for configuring a VC3 resource via vc3-resource-tool

### plugin-manager
* The plugin manager is a 3rd-party small utility for quickly constructing plugin objects from configuration input.

### request ID
* Unique identifier for a virtual cluster request.

### static infrastructure
* A set of long-running services, such as the Info Service, Master, etc.

### vc3-application
* One of the supported middleware applications to be deployed as an overlay defining a virtual cluster.

### vc3-builder
* Pilot-like executable that prepares an environment for middleware and user applications. The vc3-builder is a pilot-like utility, submitted to resource targets, which programmatically satisfies all requested dependencies before handing off control to the middleware layer. Its special feature is the ability to satisfy dependencies in different ways on different targets, depending on what it finds, e.g. it can tell if a dep is already satisfied, can download a pre-built library, or dynamically compile a dep if needed. Several builders can simultaneously satisfy dependencies in parallel on a resource (provided a shared filesystem).

### vc3-client
* Package containing the VC3-aware library for creating, listing, updating, and deleting entities within the infoservice. It also contains a command line interface to the library.

### vc3-core
* The VC3 component that coordinates activity within the dynamic infrastructure. One vc3-core exists per virtual cluster Request during its lifecycle. A vc3-core will typically start a vc3-factory, along with any central components the cluster will need (e.g. an HTCondor collector/negotiator/schedd, a WorkQueue catalog, or a Squid server).

### vc3-master
* Package containing the long-running daemon, running on the static infrastructure, that manages the lifecycle of all virtual cluster Requests. The vc3-master is a long-running daemon, running on the static infrastructure, that manages the lifecycle of all virtual cluster Requests. It polls the infoservice for new Requests, and spawns vc3-core instances on the dynamic infrastructure to service them. It also handles the generation and processing of all derived entities within the infoservice tree.

### vc3-release
* This is a developer package that contains various setup and test utilities, and artifacts needed to create and use a YUM RPM repository.

### vc3-resource-tool
* The vc3-resource-tool is a utility to be run by end users on resource targets in order to pair and enable them for usage by the VC3 system.

