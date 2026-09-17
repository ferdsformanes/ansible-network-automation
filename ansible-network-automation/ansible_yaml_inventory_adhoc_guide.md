# Run Your First Ansible Ad Hoc Command Using a YAML-Format Inventory

An Ansible inventory file allows you to store your network devices in one place instead of specifying individual IP addresses in every command.

## 1. Create the Inventory File

Create a file called:

    inventory.yml

Add your Cisco devices:

    all:
      children:
        cisco_ios:
          hosts:
            R1:
              ansible_host: 192.168.100.24
            S1:
              ansible_host: 192.168.100.26
          vars:
            ansible_connection: ansible.netcommon.network_cli
            ansible_network_os: cisco.ios.ios
            ansible_user: cisco

## 2. Test the Inventory

Run:

    ansible-inventory -i inventory.yml --list

This displays the devices and variables defined in the inventory.

You can also use:

    ansible-inventory -i inventory.yml --graph

This displays the inventory structure in a tree-like format.

## 3. Run an Ad Hoc Command

Run the following command:

    ansible cisco_ios -i inventory.yml -m cisco.ios.ios_command -a "commands='show ip int brief'" -k

### What the options mean

- `cisco_ios` - The inventory group to target
- `-i inventory.yml` - Specifies the YAML inventory file
- `-m cisco.ios.ios_command` - Uses the Cisco IOS command module
- `-a` - Specifies the module arguments
- `commands='show ip int brief'` - Cisco IOS command to execute
- `-k` - Prompts for the SSH password

## 4. Run Against All Devices

You can also use `all` instead of the group name:

    ansible all -i inventory.yml -m cisco.ios.ios_command -a "commands='show ip int brief'" -k

This runs the command against every device defined in the inventory.

## 5. Inventory Structure

The YAML inventory is organized hierarchically.

    all:
      children:
        cisco_ios:
          hosts:
            router1:
              ansible_host: 192.168.100.24
            router2:
              ansible_host: 192.168.100.26
          vars:
            ansible_connection: ansible.netcommon.network_cli
            ansible_network_os: cisco.ios.ios
            ansible_user: cisco

### `all`

The top-level `all` group contains all hosts in the inventory.

### `children`

The `children` section defines groups inside `all`.

### `cisco_ios`

This is the group containing your Cisco IOS devices.

### `hosts`

The `hosts` section contains the individual devices:

    hosts:
      router1:
        ansible_host: 192.168.100.24
      router2:
        ansible_host: 192.168.100.26

### `vars`

The `vars` section contains variables that apply to all devices in the `cisco_ios` group:

    vars:
      ansible_connection: ansible.netcommon.network_cli
      ansible_network_os: cisco.ios.ios
      ansible_user: cisco

## Result

Instead of putting IP addresses directly in every command:

    ansible all -i "192.168.100.24,192.168.100.26," ...

You can simply use:

    ansible cisco_ios -i inventory.yml ...

This makes your Ansible commands cleaner and makes it easier to manage additional network devices.

The YAML inventory also provides a structured format that can be useful as your inventory becomes larger and more complex.

## INI vs YAML

The key difference is the inventory syntax.

### INI

    [cisco_ios]
    router1 ansible_host=192.168.100.24

### YAML

    cisco_ios:
      hosts:
        router1:
          ansible_host: 192.168.100.24

Your Ansible command itself does not fundamentally change. You mainly change the inventory filename from `inventory.ini` to `inventory.yml`.
