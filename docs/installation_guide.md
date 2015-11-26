# Pre-requirements 

* OpenStack
  * You can use any version of OpenStack, however *we recommend OpenStack kilo or above*. Reason for this is that heat includes the means to reconfigure a VM at runtime.
* OpenShift v2 or v3
  * You can choose to install either OpenShift v2 or v3, however *we recommend installing v3*. The reason for this is the performance of v3 is better than that of v2.

# Install OpenStack
A full guide to installing OpenStack is beyond this guide. However, the use of [Redhat's packstack](https://openstack.redhat.com/Quickstart) is often used by us to quickly install either an OpenStack cluster or all-in-one local installation in a VM.

## Install OpenShift v2

```
wget https://install.openshift.com/portable/oo-install-origin-m4.tgz
tar zxvf oo-install-origin-m4.tgz

vi oo_install_configure_master.ops.cloudcomplab.ch.pp

puppet apply oo_install_configure_master.ops.cloudcomplab.ch.pp

sed -i s/processes=2/processes=1/g /usr/libexec/openshift/cartridges/python/usr/versions/2.7/etc/conf.d/performance.conf.erb

$ reboot

$ oo-diagnostics
```

# Install OpenShift v3
We have documented the [installation of OpenShift v3 here](http://blog.zhaw.ch/icclab/installing-openshift-origin-v3-on-openstack/).

# Install Cloud Controller
The installation of the Cloud Controller is described [here](https://github.com/icclab/hurtle_cc_api/blob/master/README.md).

# Install a Service Manager
The installation of a Serivce manager is described [here](https://github.com/icclab/hurtle_sm).

# Develop a Serivce Orchestrator
We recoommend you take a look at the sample Service Orchestrator first, it can be found [here](https://github.com/icclab/hurtle_sample_so).

Next, follow [those](https://github.com/icclab/hurtle/blob/master/docs/how_to_write_a_hurtle_service.md) steps to write your own Service Orchestrator.
