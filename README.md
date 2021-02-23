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

192.168.125.240	galaxyng
```

Create working directory:

```
$ mkdir work && cd work
```

Create `install.yml` for pulp_installer:

```
---
- hosts: all
  vars:
    pulp_settings:
      secret_key: changeme
      content_origin: "https://galaxyng"
    pulp_default_admin_password: changeme
    pulpcore_version: 3.8.1
    pulp_install_plugins:
      galaxy-ng:
        version: 4.2.2
      pulp-ansible:
        version: 0.7.0
      pulp-container:
        version: 2.3.0
  roles:
    - pulp.pulp_installer.pulp_all_services
  environment:
    DJANGO_SETTINGS_MODULE: pulpcore.app.settings
```

Create inventory file:

```
galaxyng
```

Create ansible.cfg file:

```
[defaults]
host_key_checking=false
```

Install pulp and plugins by pulp_installer:

```
$ ansible-playbook -i inventory install.yml -u redhat --ask-pass --ask-become-pass
```

Create `post_install.yml`:

```
---
- hosts: all
  vars:
    pulp_url: "http://127.0.0.1:24817"
    pulp_admin_username: admin
    pulp_default_admin_password: changeme
    pulp_validate_certs: false
  roles:
    - ansible.galaxy_collection.post_install_config
  pre_tasks:
    - name: Clean OpenAPI cache
      file:
        path: "{{ lookup('env', 'XDG_CACHE_HOME') | default('~/.cache') }}/squeezer"
        state: absent
        
  post_tasks:
    - name: Get services
      service_facts:

    - name: Restart all pulpcore* services
      systemd:
        daemon_reload: true
        name: "{{ item.value.name }}"
        state: restarted
      when: "item.value.name != 'pulpcore-worker@.service'"
      with_dict: '{{ ansible_facts.services|dict2items|selectattr("key", "contains", "pulpcore")|list|items2dict }}'
      become: true
```

Postinstall process:

```
$ ansible-playbook -i inventory post_install.yml -u redhat --ask-pass --ask-become-pass
```


# References
- [End User Installation](https://github.com/ansible/galaxy_ng/wiki/End-User-Installation)
- [Installing Pulp and galaxy NG from source](https://github.com/ansible/galaxy_ng/wiki/Installing-from-source)
- [Ansible Installer](https://pulpproject.org/ansible-installer/)
