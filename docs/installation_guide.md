#TODO
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
```
yum install -y python pip && pip install pyssf
yum install -y python-pip && pip install pyssf
lokkit -p 8888:tcp
git clone https://github.com/icclab/hurtle_cc_api.git
vi hurtle_cc_api/etc/defaults.cfg
```
