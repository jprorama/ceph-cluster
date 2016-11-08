# ceph-cluster
Ansible managment harness for a Ceph cluster.

This provides utilities for configuring a ceph cluster that is managed from an
admin node via ceph-ansible, for example [RedHat Ceph 2.0](https://access.redhat.com/products/red-hat-ceph-storage).

These utilities focus on the cluster management tasks that fall outside of the
ceph provisioning and update steps managed by [ceph-ansible](ceph/ceph-ansible).

## Setup Steps

### Create an ansible environment
```sh
virtualenv venv
. venv/bin/activate
pip install ansible
```

### Create a hosts file
Copy the hosts.sample file to hosts and replace it with your cluster hosts.
Just replace the ansible_host and ansible_user lines to match the ssh parameters
you need to connect to your cluster environment.  The head
node is the bastion ssh server so all access will be relative to that node.
Note, the ansible-user matches the ceph-ansible user used by ceph-ansible.

Copy your ssh public key to the admin node account you will use to manage the
cluster.

Test ansible setup with:
```sh
ansible -i hosts -m ping admin
```

You should receive a pong response from the admin node.
