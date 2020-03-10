# Ansible Collection - Openstack

![](https://img.shields.io/github/release/Pandemonium1986/ansible-collection-openstack.svg)
![](https://img.shields.io/github/repo-size/Pandemonium1986/ansible-collection-openstack.svg)
![](https://img.shields.io/github/release-date/Pandemonium1986/ansible-collection-openstack.svg)
![](https://img.shields.io/github/license/Pandemonium1986/ansible-collection-openstack.svg)

This [Ansible Collection](https://docs.ansible.com/ansible/latest/user_guide/collections_using.html) "openstack" contains roles and playbook to deploy and configured ressources, vms, and requirements to manage a openstack tenant.

## Getting Started

This collection contains the following ressources.

| Ressources                  | Comment                                                                        | Privilege |
| :-------------------------- | :----------------------------------------------------------------------------- | :-------: |
| **roles/pip**               | Install and configures python pip globally.                                    |    true   |
| **roles/os_requirements**   | Install openstacksdk and checks if clouds.yaml is available to the local user. |   false   |
| **roles/os_appliances**     | Generate resources in an openstack tenant from the local user's runtime.       |   false   |
| **roles/os_vms**            | Deploying vms in the openstack tenant from the local 's runtime.               |   false   |
| **playbooks/openstack.yml** | A simple example.                                                              |    true   |

### Prerequisites

The only prerequisite is to have an [Ansible](https://docs.ansible.com/ansible/latest/installation_guide/index.html) >= 2.9

### Installing

```sh
ansible-galaxy collection install pandemonium1986.openstack
```

## Deployment

First of all you must have a cloud.yaml file wich contains all information needed to connect to your openstack tenant.  
Default location of cloud.yaml is `"~/.config/openstack/clouds.yaml"`.  
For more information please visiting [Configuring OpenStack SDK Applications](https://docs.openstack.org/openstacksdk/latest/user/config/configuration.html)  

Next, you need to create a playbook that may be briefly similar to this one :

```yaml
---
- name :                                     Openstack vms instanciation
  hosts:                                     local
  become:                                    true
  collections:
   - pandemonium1986.openstack
  vars:
    openstack_users:
      - pandemonium
    openstack_project_name:                  "pandama"
    openstack_vms:
      - { name: "pandama01", flavor: "large", image: "debian10", auto_ip: true, delete_fip: true, volume_size: 30 }
      - { name: "pandama02", flavor: "large", image: "debian10", auto_ip: true, delete_fip: true, volume_size: 30 }
      - { name: "pandama03", flavor: "large", image: "debian10", auto_ip: true, delete_fip: true, volume_size: 30 }

  tasks:
    - import_role:
        name:                                pip
    - import_role:
        name:                                os_requirements
      vars:
        osrequirements_users:                "{{ openstack_users }}"
    - import_role:
        name:                                os_appliances
      vars:
        osappliances_users:                  "{{ openstack_users }}"
        osappliances_openstack_project_name: "{{ openstack_project_name }}"
    - import_role:
        name:                                os_vms
      vars:
        osvms_users:                         "{{ openstack_users }}"
        osvms_openstack_project_name:        "{{ openstack_project_name }}"
        osvms_vms:                           "{{ openstack_vms }}"
```

Available variables are :

```yaml
osrequirements_users:
  - pandemonium
osrequirements_oscli_config_file: "~/.config/openstack/clouds.yaml"

osappliances_users:
  - pandemonium
osappliances_openstack_project_name: "pandama"
osappliances_os_router_network:      "external-public"

osvms_users:
  - pandemonium
osvms_openstack_project_name: "pandama"
osvms_vms:
  - { name: "default", flavor: "tiny", image: "cirros", auto_ip: true, delete_fip: true, volume_size: 10 }  
```

## Contributing

Currently, all the roles (inside `roles/`) are Git submodules, and work on the roles themselves should take place in the upstream Role repository. At some point, the roles might move into this repository for their canonical home.

Each role can be tested independently via molecule.  

## Authors

-   **Michael Maffait** - _Initial work_ - [Pandemonium1986](https://github.com/Pandemonium1986)

## License

This project is licensed under the MIT License - see the [LICENSE](./LICENSE) file for details
