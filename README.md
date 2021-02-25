# Getting Started GalaxyNG

## Installation steps from source code on CentOS 8

Install Ansible to user spece by pip3:

```
$ sudo dnf install epel-release
$ sudo dnf groupinstall "Development Tools"
$ sudo dnf install python3 python3-devel sshpass
$ pip3 install --user ansible-base==2.10.6 ansible==2.10.6
```

Install pulp_installer and requirements by Galaxy:

```
$ ansible-galaxy collection install pulp.pulp_installer
$ ansible-galaxy collection install -r ~/.ansible/collections/ansible_collections/pulp/pulp_installer/requirements.yml
$ ansible-galaxy role install -r ~/.ansible/collections/ansible_collections/pulp/pulp_installer/requirements.yml
$ ansible-galaxy collection install ansible.galaxy_collection
```

Create `/etc/hosts` file:

```
127.0.0.1   localhost localhost.localdomain localhost4 localhost4.localdomain4
::1         localhost localhost.localdomain localhost6 localhost6.localdomain6

192.168.125.240	galaxyng  # for example
```

Cloning galaxy-demo repos and launch install and post_install playbooks by ansible-playbook command:

```
$ git clone https://github.com/saito-hideki/galaxyng-demo.git
$ cd galaxyng-demo/demo/
$ ansible-playbook -i inventory install.yml -u <YOUR_USER> --ask-pass --ask-become-pass
$ ansible-playbook -i inventory post_install.yml -u <YOUR_USER> --ask-pass --ask-become-pass
```
Finally, you can access GalaxyNG dashboard via web-browser:

```
https://galaxyng
```

# References
- [End User Installation](https://github.com/ansible/galaxy_ng/wiki/End-User-Installation)
- [Installing Pulp and galaxy NG from source](https://github.com/ansible/galaxy_ng/wiki/Installing-from-source)
- [Ansible Installer](https://pulpproject.org/ansible-installer/)
