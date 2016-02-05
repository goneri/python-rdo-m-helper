# Openstack Python-RDO-M-Helper

## Docs

[http://rdo-m-helper.readthedocs.org](http://rdo-m-helper.readthedocs.org)


## Usage

### 1. Source your Openstack openrc file

    $ source ~/openrc.sh

### 2. Install the tool in a virtualenv

    $ git clone http://softwarefactory-project.io/r/python-tripleo-wrapper
    $ mkdir jubilant-testing
    $ virtualenv jubilant-testing
    $ cd jubilant-testing
    $ source ./bin/activate
    $ pip install -e ../python-tripleo-wrapper

### 3. Run chainsaw command

First edit the configuration file 'tripleowrapper.conf', there is a
sample file in the root directory. Then run the command.

    $ chainsaw --config-file /path/to/tripleowrapper.conf


### 4. API usage

The repositories are described in this kind of structure:

```yaml
- {type: rhsm_channel, name: rhel-7-server-rpms}
- {type: rhsm_channel, name: rhel-7-server-optional-rpms}
- {type: rhsm_channel, name: rhel-7-server-extras-rpms}
- type: yum_repo
  content: |
      [RH7-RHOS-8.0]
      name=RH7-RHOS-8.0
      baseurl=http://192.168.1.2/rel-eng/OpenStack/8.0-RHEL-7/2016-01-22.1/RH7-RHOS-8.0/x86_64/os/
      gpgcheck=0
      enabled=1
  dest: /etc/yum.repos.d/rhos-release-8.repo
- type: yum_repo
  content: |
      [RH7-RHOS-8.0-director]
      name=RH7-RHOS-8.0-director
      baseurl=http://192.168.1.2/rel-eng/OpenStack/8.0-RHEL-7-director/2015-12-03.1/RH7-RHOS-8.0-director/x86_64/os/
      gpgcheck=0
      enabled=1
  dest: /etc/yum.repos.d/rhos-release-8-director.repo
```

In this example, we use instack-virt-setup in a Nova virtual machine (nested KVM).

```python
host0 = deploy_host0(
    os_auth_url, os_username, os_password,
    os_tenant_name, config)
host0.enable_repositories(
    repositories)
host0.install_nosync()
host0.create_stack_user()
host0.deploy_hypervisor()

# Our hypervisor is ready, we can now create the undercloud VM
undercloud = host0.instack_virt_setup(
    guest_image_path,
    guest_image_checksum,
    rhsm_login='some login',
    rhsm_password='some password')
undercloud.enable_repositories(repositories)
undercloud.install_nosync()
undercloud.create_stack_user()
undercloud.install_base_packages()
undercloud.clean_system()
undercloud.yum_update()
undercloud.install_osp()
undercloud.start_undercloud(
    guest_image_path,
    guest_image_checksum,
    files)
undercloud.start_overcloud()
```

## Run unit tests

    $ tox
