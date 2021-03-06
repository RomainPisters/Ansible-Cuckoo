## Synopsis

This is a fully functional updated standalone Ansible Playbook for building a Cuckoo Sandbox server out of box. It is successfully tested on a _Ubuntu 18.04(bionic)AMD64_ with the following capabilities are included:

- Cuckoo Sandbox v2.0.6 inside virtual environment
- Yara v3.9
- Volatility v2.6
- Suricata IDS v4.1
- Java v8 and Elasticsearch v5.6
- Moloch v1.8
- Support for vSphere(should work for ESXi as well)
- Django-based Web Interface and MongoDB
- PostgreSQL for simultanous analyzes on multiple sandboxes
- SSDeep
- Tcpdump
- M2Crypto
- Tor
- INetSim
- Virtualbox

## Prerequisites
The playbook automatically integrates Cuckoo to systemd. It creates the following services: cuckoo.service, cuckoo_api.service, cuckoo_web.service and cuckoo_rooter.service. If you dont have a vpn to route traffic through, disable the Openvpn role in `cuckoo-playbook/site.yml` and remove openvpnclient.service from `cuckoo-playbook/roles/start_up_cuckoo/files/cuckoo_rooter.service` at the top of the file, otherwise Cuckoo will fail to start.

It is very important that you change the config files before you start the playbook.

- `cuckoo-playbook/inventories/production/hosts` (for remote installation) or `cuckoo-playbook/inventories/staging/hosts` (for local installation)

- `cuckoo-playbook/inventories/production/hosts`

- `cuckoo-playbook/roles/cuckoo/files/cuckoo.conf`

- `cuckoo-playbook/roles/cuckoo/files/vsphere.conf`

- `cuckoo-playbook/roles/cuckoo/files/routing.conf`

- `cuckoo-playbook/roles/elasticsearch/files/elasticsearch.yml`

- `cuckoo-playbook/roles/suricata/files/suricata`

- `cuckoo-playbook/roles/tor/files/torrc`

- Change variables in `cuckoo-playbook/inventories/production/group_vars/all` or `cuckoo-playbook/inventories/staging/group_vars/all` depending on your setup(remote/local) to reflect correct distribution and NIC names.

- Change IP-address in `cuckoo-playbook/roles/start_up_cuckoo/files/cuckoo_api.service` and `cuckoo-playbook/roles/start_up_cuckoo/files/cuckoo_web.service` to reflect your setup.

Please add the cuckoo VM image in .ovf format in the `cuckoo-playbook/roles/virtualbox/files/cuckoovm/` directory. Then this will be automatically placed in the correct directory.

## Installation

The playbook aims to succesfully install to a fresh install of _Ubuntu 18.04 Server(amd64)_ with the following package options:

- openssh-server
- python 2.7 (symlinked to `python` -> `ln -s /usr/bin/python2.7 /usr/bin/python`)

After the base OS install a dist-upgrade was conducted:

`apt update & apt dist-upgrade`

Installation of the Cuckoo environment is done with the following steps:
1. Clone this repository;

2. Refer to prerequisites for files to edit before running the playbook.

3. Replace the placeholders in **cuckoo-playbook/inventories/production/hosts** with the correct ones for your installation, where:

    - **HOST** is the IP address of the server to install Cuckoo to
    - **ADMIN** is a user with sudo privileges on the server
    - **PASSWORD** is the user _ADMIN_ password

4. Run the following command inside cuckoo-playbook folder:

```
ansible-playbook -i inventories/production site.yml
```

By default, Cuckoo will be installed to `/opt/cuckoo` inside a virtual environment and a `cuckoo` user and group will be created. These values can be modified at **group_vars** file:

    cuckoo_user: 'cuckoo'
    cuckoo_dir: '/opt/cuckoo'

## Usage

Once the installation has completed, Suricata, Moloch, Cuckoo Rooter alongside API, Web interface and Cuckoo itself will start up automatically.

## Disclaimer

Prepped for Virtualbox environments
