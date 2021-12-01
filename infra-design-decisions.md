# Infrastructure Design Decisions - Architecture Decision Records

## Use an isolated network for the factory

_Status:_ Accepted

_Context:_
All options should be kept open, including PXE boot of the Fitlet

_Options:_
No real options exist apart from running the Fitlet in a separted network, and connecting the Single Node Openshift to it

_Outcome:_
Use an isolated network for the Factory network

_Consequences:_
Some additional hardware will be required to create the solution

## Design the setup as a portable solution

_Status:_ Accepted

_Context:_

The NUC and the Fitlet must be able to communicate, at least the NUC will require internet access.

The hardware will likely be installed in one of the team members home, but it should be possible to move it easily to someone elses home, or to the office.

The solution should be easy to put in operation (add power, connect network)

_Options:_

- Use a cross cable to connect both HW modules directly, and configure the NUC to use Wifi
- Make use of a switch (not provided) and an external router (not provided)
- Make use of a switch (not provided) and use one of the NUC as the router
- Make use of a switch (not provided) and use the Fitlet as the router

_Outcome:_

Configuring the Wifi on the NUC is too complicated without a GUI, GUI not considered part of the option. Invalidates option 1

An external router is not available, invalidates option 2

The Fitlet has 2 ethernet portrs, but it is not meant to do more than control the T-Shirt machinery

_Consequences:_

An additonal network dongle is needed to use on to the NUC

The Single Node Openshift installation should either handle this network configuration, or the SNO should be a VM

## Use NUC as KVM Hypervisor

_Status:_ Accepted
_Context:_

Some additional functionality is required for network configuration.
This may just be a router, or potentially more, which is not easily configurable on the CoreOS based Single Node OpenShift installation

_Options:_

- Use more hardware
- Use the NUC as KVM based Hypervisor with the SNO install on one VM, network services on second VM)
- Use the NUC as KVM based Hypervisor with the SNO install on a VM, network services native on the NUC

_Outcome:_
To preserve resources, a VM will be created for the SNO installation, but the other services will be installed on the NUC itself

_Consequences:_

We will not have native performance and some memory should be preserved for the Hypervisor and the Services (ex. 48GiB for the SNO, 16GiB for the Hypervisor, equally so for storage: ex. 120GiB for the SNO, the rest for the services)

## Network access to the factory

_Status:_ Accepted

_Context:_
All team members should be able to connect to the Factory Solution easily

_Options:_

- Member hosting the solution provides ad-hoc connectivity
- Access over VPN
- Access over SSH Tunnels, over a cloud instance

_Outcome:_

Option 1 and 2 may not be feasable for all team members

Option 3 is chosen because it may not require any changes when the solution is moving from one person to the next

_Consequences:_

While no-cost instances are available, this may incur a small cost

Additional software (SwitchyOmega) should be installed on member PCs

To be workable, the solution should be documented so team members with less thorough networking skills

## Use fully automated integration and deployment pipelines

_Status:_ Accepted

_Context:_

Software will be built. To move fast, this should be deployed automatically

_Options:_

- Manual build, push, etc.
- Use CI/CD Tooling

_Outcome:_

Option 1 will likely be used for some initial code, but is not a solution, long term

We will aim for having a fully automated deployment

_Consequences:_

CI/CD tools are required

## Use Tekton to implement all pipelines

_Status:_ Accepted

_Context:_

A tool for CI/CD is needed.

_Options:_

- Use Tekton, available as OpenShift Pipelines, installable by an operator
- Use another on-premise tool
- Use a Cloud-based tool

_Outcome:_

Tekton will be used, to reduce additional resources, or additional costs.

_Consequences:_

Tekton requires dynamic storage on the OpenShift cluster

## Use Ansible to deploy containers on the Fitlet

_Status:_ Accepted

_Context:_

The Fitlet is installed with RHEL for Edge.
New versions of the software should be deployed using tooling.

_Options:_

- Ansible
- Other?

_Outcome:_

We will be using Ansible to deploy and setup the container processes on the Fitlet.

_Consequences:_

Some additional code should be written by infrastructure specialists

## Use Gitops for OpenShift configuration

_Status:_ Accepted

_Context:_

Some additional components should be deployed on OpenShift.

_Options:_

- Manual deployment
- Gitops

_Outcome:_

We will try to maximize the use of GitOps even for infrastructure components

_Consequences:_

This will take a bit more time to setup, compared to `oc apply -f` or `helm install`.

## Use Gitops for Tekton Pipelines

_Status:_ Accepted

_Context:_

Multiple tasks and multiple pipelines should be created.
These will be in source control to keep track of changes.

Applying changes to the cluster should be trivial.

_Options:_

- Manual deployment
- Gitops

_Outcome:_

We will try to maximize the use of GitOps even for infrastructure components

_Consequences:_

This will take a bit more time to setup, compared to `oc apply -f`
