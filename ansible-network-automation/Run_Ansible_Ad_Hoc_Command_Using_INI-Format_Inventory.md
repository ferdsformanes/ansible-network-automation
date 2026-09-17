# Run Your First Ansible Ad Hoc Command Using an INI-Format Inventory

An Ansible inventory file allows you to store your network devices in one place instead of specifying individual IP addresses in every command.

## 1. Create the Inventory File

Create a file called:

    inventory.ini

Add your Cisco devices:

    [cisco_ios]
    router1 ansible_host=192.168.100.24
    router2 ansible_host=192.168.100.26

    [cisco_ios:vars]
    ansible_connection=ansible.netcommon.network_cli
    ansible_network_os=cisco.ios.ios
    ansible_user=cisco

## 2. Test the Inventory

Run:

    ansible-inventory -i inventory.ini --list

This displays the devices and variables defined in the inventory.

## 3. Run an Ad Hoc Command

Run the following command:

    ansible cisco_ios -i inventory.ini -m cisco.ios.ios_command -a "commands='show ip int brief'" -k

### What the options mean

- `cisco_ios` - The inventory group to target
- `-i inventory.ini` - Specifies the inventory file
- `-m cisco.ios.ios_command` - Uses the Cisco IOS command module
- `-a` - Specifies the module arguments
- `commands='show ip int brief'` - Cisco IOS command to execute
- `-k` - Prompts for the SSH password

## 4. Run Against All Devices

You can also use `all` instead of the group name:

    ansible all -i inventory.ini -m cisco.ios.ios_command -a "commands='show ip int brief'" -k

This runs the command against every device defined in the inventory.

## 5. Inventory Structure

The inventory is organized into groups.

    [cisco_ios]
    router1 ansible_host=192.168.100.24
    router2 ansible_host=192.168.100.26

    [cisco_ios:vars]
    ansible_connection=ansible.netcommon.network_cli
    ansible_network_os=cisco.ios.ios
    ansible_user=cisco

The `[cisco_ios]` section contains the devices.

The `[cisco_ios:vars]` section contains variables that apply to all devices in the `cisco_ios` group.

## Result

Instead of putting IP addresses directly in every command:

    ansible all -i "192.168.100.24,192.168.100.26," ...

You can simply use:

    ansible cisco_ios -i inventory.ini ...

This makes your Ansible commands cleaner and makes it easier to manage additional network devices.
