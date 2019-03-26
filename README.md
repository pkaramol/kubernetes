# Local / Remote non-HA kubernetes cluster (WIP)

Project that uses `vagrant` and `ansible` to create a local as also remote
`kubernetes` cluser with `kubeadm` in non High Availability mode, i.e. with a 
single master that also incorporates the `etcd` database.

Provided that it works as expected, you can use it to set up locally a 
non-single node (as provided by `microk8s` and `minikube`) `kubernetes` cluster;


## Prerequisites


##  Pre-Requisites (host machine)

[__Virtualbox__](https://www.virtualbox.org/wiki/Linux_Downloads)

[__Vagrant__](https://www.vagrantup.com/docs/installation/)

[__Ansible__](https://docs.ansible.com/ansible/latest/installation_guide/intro_installation.html)

[__kubectl__](https://kubernetes.io/docs/tasks/tools/install-kubectl/)


## Usage

`vagrant up` should give you a local `kubernetes` cluster with which you can 
interact via `kubectl`

Make sure you override the `if_name` role variable with the interface name whose
IP you want to use for intra-node communication.

You can tweak the `NODES` variable in `Vagrantfile` to account for the number of
nodes you wish.

Once the provisioning is over, you should be able, after executing

```
kubectl proxy
```

on your host machine, to access the admin dashboard [here](http://localhost:8001/api/v1/namespaces/kube-system/services/https:kubernetes-dashboard:/proxy/)

follow [these](https://github.com/kubernetes/dashboard/wiki/Creating-sample-user) 
instructions to create the necessary role/rolebinding and grab the token that 
will allow you admin access to the UI.

Creating a playbook and running against normal (i.e. non-vagrant) hosts should
work, provided that the inventory file used groups nodes in a `nodes` group as 
also `masters` in a `masters` group. __NOT TESTED YET__

You should also tweak the resources used to spin up the `vagrant` VMs in the 
`Vagrantfile` to match your host machine's capacity; as in ...

```
master.vm.provider "virtualbox" do |vb|
    vb.cpus = "2"
    vb.memory = "4096"
end
```


## Warning(s) !

The role is not (yet at least) idempotent so re-runing it over an existing 
cluster that you created using this functionality will most likely __reset__ the 
existing cluster (despite checks being performed on whether `6443` is in use 
or a cluster is already running on the particular master)

## TODOS

- Test it against non vagrant hosts/vms etc.
- Add other networking options besides `flannel`
- Fail / Exit if cluster exists and / or `6443` on master is in use
- Add cluster health check at the end of the role
- [...] other