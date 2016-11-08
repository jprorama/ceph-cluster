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

### Set the cluster admin users

Copy the site_vars.yml.sample file to site_vars.yml and update the list of
cluster_admins for you cluster.  These are the accounts that will have
passwordless root access to your admin node and will be therefore own your cluster.

Update the cluster_admins
```sh
ansible-playbook -i hosts set-cluster-admins.yml --ask-become-pass
```

Note, this is a bootstrap to passwordless access, so a normal su and password method
are used.

### Add client keys to the ceph-ansible account (optional)

Managing the ceph cluster leverages the ceph-ansbile user set up during the
initial cluster install steps.   The add-client-key playbook simplifies adding
the public key of the cluster admin account in ~/.ssh/id_rsa.pub to the already
configured internal ceph-ansible user account.  Update the site_vars to define
the account name which matches the internal cluster configuration.

```sh
ansible-playbook -i hosts add-client-key.yml
```

After this update the cluster admin should be able to ping all nodes in the
cluster, given a working hosts file configuration.  The following should return
a pong response from all cluster nodes including the admin (head) node.

```sh
ansible -i hosts -m ping all --become
```
