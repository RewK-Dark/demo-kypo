# kypo-lite
This guide aims to make the deployment of the KYPO Cyber Range Platform as simple as possible for the evaluation and creation of KYPO content. For production workloads, standard deployment with
external OpenStack deployment is recommended due to limited resources on a single host and slow performance of double to triple virtualization used by this guide.

The following steps will deploy OpenStack into a single KVM VM and set up KYPO inside the OpenStack instance.

Requirements:
* Ubuntu 22.04
* 8 VCPU, 48 GB RAM, 250 GB HDD (4 VCPU, 48 GB RAM is minimum for basic functionality)
* Platforms:
    * physical server
    * desktop
    * VM with nested virtualization enabled
    * cloud flavors with virtualization support - KYPO lite works well with Google Cloud Platform instances **n2-highmem-4** and **n2-highmem-8**. You can use sample [Terraform](tf-gcp-vm) code to deploy a virtual machine for KYPO-lite to the Google Cloud Platform

## Deployment

```
sudo apt install -y qemu qemu-kvm libvirt-daemon libvirt-clients bridge-utils virt-manager vagrant vagrant-libvirt
vagrant up
```

For 4 CPU run instead of vagrant up:
```
CPU=4 vagrant up
```

The URL of the KYPO portal is shown at the end of the provisioning. In case of deployment on your desktop, you can access the URL directly.

In case of running Vagrant instance on a remote host, it's convenient to tunnel communication over ssh, e.g. with sshuttle:
```
sshuttle -r root@host 10.1.2.0/24
```
where host is IP/FQDN of your remote server.

## Adding own sandbox definition to internal Git

1. Add your sandbox definition to [git-internal-repos.yaml](git-internal-repos.yaml) (similar as line 16).
2. `vagrant ssh -- -t 'sudo su'`
3. `kubectl patch deployment git-internal -p "$(cat /vagrant/git-internal-repos.yaml)" -o yaml`

## Additional information

OpenStack API & GUI is running on IP **10.1.2.9**

### OpenStack CLI access
OpenStack CLI is available inside VM. Execute:

`vagrant ssh -- -t 'sudo su'`

and you can start using CLI commands (RC file with credentials is already sourced).

### OpenStack GUI access
To access the Horizon portal, enter http://10.1.2.9 into the browser. Login is admin.
Password can be retrieved from VM with command:

`grep "OS_PASSWORD" /etc/kolla/admin-openrc.sh`

### KVM environment

The installation will create VM with IP **10.1.2.10**.

The installation will create a 10.1.2.0/24 bridged network that is fully accessible from the host running the KVM instance.
