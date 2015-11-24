#TODO

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

## Install OpenShift v3
We have documented the [installation of OpenShift v3 here](http://blog.zhaw.ch/icclab/installing-openshift-origin-v3-on-openstack/).

## Install Cloud Controller
```
yum install -y python pip && pip install pyssf
yum install -y python-pip && pip install pyssf
lokkit -p 8888:tcp
git clone https://github.com/icclab/hurtle_cc_api.git
vi hurtle_cc_api/etc/defaults.cfg
```
