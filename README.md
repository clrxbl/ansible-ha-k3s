# ansible-ha-k3s
Ansible playbook for an HA K3s cluster running etcd

**CAUTION:** This is the first time I've used Ansible. Potential issues may arise here and there, improvement PRs are very welcome. No support is given.

## Requirements

Target machines are expected to run Debian. This Ansible playbook was tested on Debian 10.4
Needs ansible & ansible-galaxy, install the k3s role using the command below.

```
ansible-galaxy install xanmanning.k3s
```

## Usage

Modify inventory/hosts.ini with your hosts & hostnames. Make sure you have access to the root user via SSH.
With the example configuration used in this repository, it is expected that there is a load balancer, in this case located at `lb.ansible.iptables.sh` for ports 2379 (etcd) & 6443 (k8s). It is also expecting a minimum of 2 masters, however this can be overriden by setting the variable `k3s_use_unsupported_config` to true.

```
ansible-playbook create-cluster.yml

Unsupported config:
ansible-playbook create-cluster.yml --extra-vars "k3s_use_unsupported_config=true"
```

After a few minutes, you should be able to SSH into any one of the servers and run `kubectl get nodes` to confirm that all nodes are working.

## Updating your cluster

K3s can handle auto-updating by itself. It is recommend to follow Rancher's guide for automated updates over here: https://rancher.com/docs/k3s/latest/en/upgrades/automated/

unattended-upgrades is installed during cluster creation which handles Debian's security updates.

To manually update all packages, upgrade packages & reboot the server upon kernel update, run:

```
For all servers in the hosts file:
ansible-playbook update-cluster.yml

For 1 specific FQDN / IP address:
ansible-playbook -i 192.168.0.55, update-cluster.yml
```

## Adding a worker

You don't want to run create-cluster.yml after you've already ran it, since this will essentially wipe the current etcd & k3s installations.
To add a new worker, use the add-worker.yml workbook. Make sure to add your new workers into the [new_worker] group in hosts.ini
Before you run the playbook, you will also need to extract the NODE_TOKEN from /var/lib/rancher/k3s/server/node_token and place it in the inventory/group_vars/new_worker file.

```
ansible-playbook add-worker.yml
```
