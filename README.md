<div align="center" >
<img src="https://raw.githubusercontent.com/icclab/hurtle/master/docs/figs/hurtle-logo.png" title="hurtle" width=250px>
<br/>
<img src="https://raw.githubusercontent.com/icclab/hurtle/master/docs/figs/hurtle-logo-text.png" title="hurtle" width=250px>
</div>

# hurtle?


Q: Why is it Called hurtle?

> A: Cos we like [turtles and recursion](https://en.wikipedia.org/wiki/Turtles_all_the_way_down).

Q: What is your motto?

> A: "Confusing name, simple orchestration".

[![Gitter](https://badges.gitter.im/icclab/hurtle.svg)](https://gitter.im/icclab/hurtle?utm_source=badge&utm_medium=badge&utm_campaign=pr-badge)

# Why hurtle?

hurtle lets you:

> offer your software as a service i.e. "hurtle it!"

Hurtle lets you automate the life-cycle management of your service, from deployment of cloud resources all the way to configuration and runtime management (e.g., scaling).

**But here comes the best part: **
> hurtle has been designed since its inception to support service composition, so that you can run complex services by (recursively!) composing simple ones. Welcome to truly modular cloud service composition! Microservices anyone?

> hurtle enables service and infrastructure orchestration to easily compose, deploy, provision and manage distributed systems

Its functionality all revolves around this idea, so the service offered is also one that can be designed with the cloud in mind, based on the [cloud-native application research of the ICCLab](http://blog.zhaw.ch/icclab/category/research-approach/themes/cloud-native-applications/).

## Where From?

hurtle has two origins in:

1. the ICCLab's [Cloud Orchestration Initiative](http://blog.zhaw.ch/icclab/category/research-approach/themes/cloud-orchestration/)
2. the telcom world and in particular [Network Function Virtualisation](https://en.wikipedia.org/wiki/Network_functions_virtualization) (NFV). Here hurtle has been used to offer services that have been to date executed directly on or embedded in hardware.

And well, it's all powered upon another hurtle ;-)

## Questions that Hurtle Aims to Address

 * How to monetise your software?
 * How to deliver your software as a service?
 * How to compose existing services?
 * How deliver and maintain reliability?

# Overview

This repository provides documentation for hurtle and
pointers to the other repositories that make up a complete hurtle system.
See the [repository list](https://github.com/icclab/hurtle/blob/master/docs/reference.md).

hurtle consists of the following components:

- [Service Manager (SM)](https://github.com/icclab/hurtle/blob/master/docs/architecture.md): receives requests for new tenant service instances → [Code](https://github.com/icclab/hurtle_sm)
- [Service Orchestrator (SO)](https://github.com/icclab/hurtle/blob/master/docs/architecture.md): manages the lifecycle of a tenant service instance → [Sample code](https://github.com/icclab/hurtle_sample_so)
- [CloudController (CC)](https://github.com/icclab/hurtle/blob/master/docs/architecture.md): manages and abstracts underlying resources and SOs → [Code](https://github.com/icclab/hurtle_cc_api)

For more details, see:

- [hurtle's Architecture](https://github.com/icclab/hurtle/blob/master/docs/architecture.md) for overall logical architecture.
- [hurtle's Technical Architecture](https://github.com/icclab/hurtle/blob/master/docs/hurtle_technical_implementation.md) for details on the implementation of the support components.
- [hurtle's How to write your Hurtle Service?](https://github.com/icclab/hurtle/blob/master/docs/how_to_write_a_hurtle_service.md) for details on the components necessary to create a hurtle manageable service. Note that a lot of information can also be found directly in the examples provided here!
- [hurtle Reference & Repolist](https://github.com/icclab/hurtle/blob/master/docs/reference.md) for an overview of each component.

# Features

 - Complete orchestration of your software
   - Resources it uses (e.g. virtual machines, networks)
   - External services it needs
 - Multi-dc/multi-region support
   - including inter-DC connectivity
 - Easy implementation of your service API - See [how to write your Hurtle Service](./docs/how_to_write_a_hurtle_service.md)
 - Guided implementation of your service instance manager
   - Many languages supported including Python, Java, Perl, PHP
   - Demo applications available
 - Scalable runtime management
 - Complete end-to-end logging of your software
 - Integration with [OpenStack](http://www.openstack.org/), [ICCLab's Joyent SDC contribs](https://github.com/icclab/sdc-heat)
 - Handle potential incidents of your software
   - Integration with [ICCLab's Watchtower (Cloud Incident Management)](https://github.com/icclab/watchtower-common)
 - Bill for your software and services
   - Integration with [ICCLab's Cyclops (Rating, charging & Billing)](https://icclab.github.io/cyclops/)
 - Leverages Open Cloud Standards ([OCCI](http://www.occi-wg.org), [OpenStack](http://www.openstack.org))

# How Does hurtle Work?

The easiest way to understand how hurtle works is through how its life cycle of an application is managed. There are 6 key phases to understand:

<div align="center">
<img src="https://raw.githubusercontent.com/icclab/hurtle/master/docs/figs/hurtle_lifecycle_2.png" alt="hurtle Text" title="hurtle">
</div>

 1. **Design**: where the topology and dependencies of each service component is specified. The model here typically takes the form of a graph.
 2. **Implementation**: This is where the developer(s) needs to implement the actual software that will be provided as a service through hurtle
 3. **Deploy**: the complete fleet of resources and services are deployed according to a plan executed by hurtle. At this stage they are not configured.
 4. **Provision**: each resource and service is correctly provisioned and configured by hurtle. This must be done such that one service or resource is not without a required operational dependency (e.g. a php application without its database).
 5. **Runtime**: once all components of an hurtle orchestration are running, the next key element is that they are managed. To manage means at the most basic level to monitor the components. Based on metrics extracted, performance indicators can be formulated using logic-based rules. These when notified where an indicator’s threshold is breached, an Orchestrator could take a remedial action ensuring reliability.
 6. **Disposal**: Where a hurtle service instance is destroyed.
 
More details are in the [logical](https://github.com/icclab/hurtle/blob/master/docs/architecture.md) and [technical](https://github.com/icclab/hurtle/blob/master/docs/hurtle_technical_implementation.md) architecture documents.

# Getting Started
## Installation
A complete installation guide can be found [here](https://github.com/icclab/hurtle/blob/master/docs/installation_guide.md)

## Vagrant
Note: The Vagrant boxes are quiet demanding (6GB and 2GB of RAM) on your System.

The Vagrant boxes give you a complete environment to play around: OpenStack, OpenShift and the hurtle-sample-so are preinstalled.


```
# Clone this repo
git clone https://github.com/icclab/hurtle.git

# Spin up the Vagrant boxes
cd vagrant 
vagrant up

# Grab a coffee, this could take a while (~3GB download)

# Once the machines are booted up, check out the hurtle-sample-so README file

```

# Roadmap

Hurtling along soon:

 - More examples including the [ cloud native Zurmo implementation from ICCLab](https://github.com/icclab/cna-seed-project)
 - Enhanced workload placement, dynamic policy-based
 - Support for docker-registry deployed containers
 - Runtime updates to service and resource topologies
 - CI and CD support
   - safe monitored dynamic service updates
 - TOSCA support
 - Support for VMware and CloudStack
 - User interface to visualise resource and services relationships
 - Additional external service endpoint protocol support

# Community & Support

[![Gitter](https://badges.gitter.im/icclab/hurtle.svg)](https://gitter.im/icclab/hurtle?utm_source=badge&utm_medium=badge&utm_campaign=pr-badge)

Report bugs and request features using [GitHub Issues](https://github.com/icclab/hurtle/issues). For additional resources, you can contact the maintainers directly. Community discussion about turtle happens in one main place:

* The *hurtle-discuss* mailing list. Once you [subscribe to the list]( https://mailman.engineering.zhaw.ch/mailman/listinfo/icclab-hurtle),
  you can send mail to the list address: [icclab-hurtle@dornbirn.zhaw.ch](mailto:icclab-hurtle@dornbirn.zhaw.ch)
  The mailing list archives are also [available on the web](https://mailman.engineering.zhaw.ch/pipermail/icclab-hurtle/).


You can follow [@hurtle_it](https://twitter.com/hurtle_it) on
Twitter for updates and of course [on the ICCLab blog](http://blog.zhaw.ch/icclab/tag/hurtle/)

## Contributing

To report bugs or request features, submit issues [here on
GitHub](https://github.com/icclab/hurtle/issues)..
If you're contributing code, make pull requests to the appropriate
repositories (see [the repo overview](https://github.com/icclab/hurtle/blob/master/docs/reference.md)).
If you're contributing something substantial, you should first contact
developers on the [hurtle-discuss mailing list](mailto:icclab-hurtle@dornbirn.zhaw.ch)
([subscribe](https://mailman.engineering.zhaw.ch/mailman/listinfo/icclab-hurtle)).

For urgent questions please contact the [maintainers](https://github.com/icclab/hurtle/blob/master/docs/maintainers.md) directly.

Hurtle repositories follow no written Guidelines to date.

## License

hurtle is licensed under the
[Apache License version 2.0](https://www.apache.org/licenses/LICENSE-2.0).
See the file LICENSE.

# Made by

<div align="center" >
<a href='http://blog.zhaw.ch/icclab'>
<img src="https://raw.githubusercontent.com/icclab/hurtle/master/docs/figs/icclab_logo.png" title="hurtle" width=400px>
</a>
</div>

# Supported by

<div align="center" >
<a href='http://blog.zhaw.ch/icclab'>
<img src="https://raw.githubusercontent.com/icclab/hurtle/master/docs/figs/mcn_logo.png" title="mobile cloud networking" width=400px>
</a>
</div>
