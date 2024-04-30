# Building An Inventory

Inventories organize managed nodes in centralized files that provide Ansible with system information and network locations. Using an inventory file, Ansible can manage a large number of hosts with a single command.

To complete the following steps, you will need the IP address or fully qualified domain name (FQDN) of at least one host system. For demonstration purposes, the host could be running locally in a container or a virtual machine. You must also ensure that your public SSH key is added to the authorized_keys file on each host.

1. Create a file named `inventory.ini` in the root directory of your Ansible project.

2. Add a new `[myhosts]` group to the `inventory.ini` file  and specify the IP address or fully qualified domain name (FQDN) of each host system.

    ```plaintext
    [myhosts]
    192.0.2.50
    192.0.2.51
    192.0.2.52
    ```

3. Verify that the inventory file is correctly formatted by running the following command:

    ```bash
    ansible-inventory --list -i inventory.ini
    ```

4. Ping `myhosts` to verify that Ansible can connect to the host systems:

    ```bash
    ansible myhosts -i inventory.ini -m ping
    ```

- You can create inventories in either INI files or in YAML. In most cases, such as the example in the preceding steps, INI files are straightforward and easy to read for a small number of managed nodes. However, for larger inventories, YAML files are more flexible and easier to manage.

## Tips for Building Inventories

- Ensure that group names are meaningful and unique. Group names are also case sensitive.

- Avoid spaces, hyphens, and preceding numbers (use floor_19, not 19th_floor) in group names.

- Group hosts in your inventory logically according to their What, Where, and When.

  - **What**: Group hosts according to the topology, for example: db, web, leaf, spine.

  - **Where**: Group hosts by geographic location, for example: datacenter, region, floor, building.

  - **When**: Group hosts by stage, for example: development, test, staging, production.

### Use metagroups

Create a metagroup that organizes multiple groups in your inventory with the following syntax:

```plaintext
metagroupname:
  children:
```

For example:

```plaintext
leafs:
  hosts:
    leaf01:
      ansible_host: 192.0.2.100
    leaf02:
      ansible_host: 192.0.2.110

spines:
  hosts:
    spine01:
      ansible_host: 192.0.2.120
    spine02:
      ansible_host: 192.0.2.130

network:
  children:
    leafs:
    spines:

webservers:
  hosts:
    webserver01:
      ansible_host: 192.0.2.140
    webserver02:
      ansible_host: 192.0.2.150

datacenter:
  children:
    network:
    webservers:
```

### Create variables

Variables set values for managed nodes, such as the IP address, FQDN, operating system, and SSH user, so you do not need to pass them when running Ansible commands.

```plaintext
webservers:
  hosts:
    webserver01:
      ansible_host: 192.0.2.140
      http_port: 80
    webserver02:
      ansible_host: 192.0.2.150
      http_port: 443
  vars:
    ansible_user: my_server_user
```
