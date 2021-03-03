# GalaxyNG installer to build Private Galaxy Site

This collection is for building Private Galaxy site.

## How to use the collection

Create an `inventory` file:

```
galaxyng
```

Download the collection and required role using `ansible-galaxy` command:

```
$ ansible-galaxy collection install saito_hideki.galaxyng_installer
$ ansible-galaxy role install geerlingguy.postgresql
```

Create `install.yml`:

```
---
- name: Install GalaxyNG to build demo site
  hosts: galaxyng
  gather_facts: true

  collections:
    - saito_hideki.galaxyng_installer

  roles:
    - preflight
    - packages
    - galaxyng
```

Launch `install.yml`:

```
$ ansible-playbook -i inventory -u <USER> --ask-become-pass install.yml
```
