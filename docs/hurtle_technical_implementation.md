# Hurtle Technical Implementation

**TODO: Add image**

Based on the architecture we designed, the implementation uses the following technologies shown below to support the lifecycle of a Hurtle service.

## Cloud Standard: OCCI
OCCI is an open cloud interface specification which forms the core of the interfaces exposed by the Service Manager, CloudController and the Service Orchestrators.

OCCI is also the specification used by the northbound interface of the CloudController.

## Code Implementation: Python & PySSF

Most of the implementation of Hurtle is written in Python. It comes with many existing libraries to the external components we use such as OpenShift or Heat. 

That said, service orchestrators written for Hurtle can use any languages supported by OpenShift, including Java or PHP. This would make is easy and possible to deploy a Java SO and there would be minimal changes to the reference implementation.

The Python Service Sharing Facility (PySSF) library is an OCCI compatible implementation. It provides an OCCI compatible frontend and provides a Python Web Server gateway Interface, WSGI, application can can run on most web frameworks. PySSF is at the core of the OCCI interfaces in Hurtle's implementation.Each service is defined by extending the OCCI core model as implemented by PySSF, which provides a model that is easy to extend and provides interoperability out of the box.


## Supporting Component: Cloud Controller

The Cloud Controller is made of a set of modules: design, deployment, provisioning, runtime and disposal. Each of the modules is modeled as a service itself. 
The CC uses many external components at various phases to realize its implementation.* Design - Provided through reusing the *OpenStack Keystone* service.* Deployment - provided by the PaaS *OpenShift* for Service Orchestrators and *OpenStack Heat* for virtual resources. OpenStack heat was extended through plugins to support the different platform in place (OpenStack, Joyent SDC).* Provisioning - Realized by different technologies to provide a diversity of choices to the service developers. Realized in the easiest form through cloud-init functionalities passed through OpenStack heat.* Runtime - Provided by OpenStack Monasca  but is pluggable and can also be realized by other means (see section 5.2.2)* Disposal - related to both deployment and provisioning so implicitly pluggableThe incoming interface to OpenShift is exposed as an OCCI interface and is used by the Service Managers to deploy resources.
## Service Development KitThe Service Development Kit was introduced to support service developers and those who write code to manage services. It serves two main purposes:* Support the basic functionalities for the lifecycle management of any service by allowing the SOs to interact with the various internal services of the Cloud Controller (CC). Again changes to the internal implementation of the CC will have no impact on the code written, as the SDK abstract the technologies used.* Provide access to a set of support services. This will allow for service developers to e.g. interact with a DNS service is a certain way, regardless how the DNS service is implemented.