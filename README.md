# ansible-ha-k3s
Ansible playbook for an HA K3s cluster running dqlite

**CAUTION:** This is the first time I've used Ansible. Potential issues may arise here and there, improvement PRs are very welcome. No support is given.

## Requirements

Target machines are expected to run Debian. This Ansible playbook was tested on Debian 10.4
Needs ansible & ansible-galaxy, install the k3s role using the command below.

```
ansible-galaxy install xanmanning.k3s
```

## Usage

Modify inventory/hosts.ini with your hosts & hostnames. Make sure you have access to the root user via SSH.

```
ansible-playbook create-cluster.yml
```

After a few minutes, you should be able to SSH into any one of the servers and run `kubectl get nodes` to confirm that all nodes are working.