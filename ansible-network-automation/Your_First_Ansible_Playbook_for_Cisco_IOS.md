# Your First Ansible Playbook for Cisco IOS

A simple guide to creating and running your first Ansible playbook against Cisco IOS devices using a **YAML inventory**.

## 1. Create the Inventory

Create a file called:

```text
inventory.yml
```

Add your Cisco devices:

```yaml
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
```

### What the inventory settings mean

* `cisco_ios` — the inventory group containing your Cisco devices.
* `ansible_host` — the IP address of each device.
* `ansible_connection` — tells Ansible to use the `network_cli` connection.
* `ansible_network_os` — tells Ansible that the devices are Cisco IOS.
* `ansible_user` — the username used to connect to the devices.

Putting the connection settings in the inventory means you don't need to specify them every time you run the playbook.

## 2. Create the Playbook

Create a file called:

```text
show_interfaces.yml
```

Add:

```yaml
---
- name: Show Cisco IOS Interface Status
  hosts: cisco_ios
  gather_facts: false

  tasks:
    - name: Run show ip interface brief
      cisco.ios.ios_command:
        commands:
          - show ip interface brief
      register: output

    - name: Display command output
      ansible.builtin.debug:
        var: output.stdout_lines
```

## 3. Run the Playbook

Run:

```bash
ansible-playbook -i inventory.yml show_interfaces.yml -k
```

Enter the Cisco device password when prompted.

Because the connection settings, Cisco IOS network OS, and username are already defined in `inventory.yml`, you don't need to specify:

```text
-c network_cli
-e "ansible_network_os=cisco.ios.ios"
-u cisco
```

on the command line.

## 4. Understand the Playbook

* `hosts: cisco_ios` — runs the playbook against all devices in the `cisco_ios` group.
* `gather_facts: false` — skips automatic fact gathering.
* `cisco.ios.ios_command` — runs commands on Cisco IOS devices.
* `show ip interface brief` — displays interface status and IP addresses.
* `register: output` — saves the command result in the `output` variable.
* `ansible.builtin.debug` — displays the result in the terminal.

## 5. Expected Result

You should see output similar to:

```text
GigabitEthernet0/0    192.168.1.1    YES    manual    up    up
GigabitEthernet0/1    unassigned    YES    unset     down  down
```

The exact output depends on your Cisco device configuration.

## 6. Inventory vs. Playbook

A useful way to think about the two files is:

### `inventory.yml`

Defines **which devices Ansible connects to and how to connect to them**.

```text
Devices
  ↓
IP addresses
  ↓
Connection method
  ↓
Network OS
  ↓
Username
```

### `show_interfaces.yml`

Defines **what Ansible should do**.

```text
Playbook
  ↓
Select Cisco devices
  ↓
Run show ip interface brief
  ↓
Display the output
```

## Summary

You have now created and executed your first Ansible playbook for Cisco IOS using a YAML inventory.

The basic workflow is:

```text
YAML Inventory → Playbook → Task → Cisco IOS Command → Output
```

Your setup now separates the **device connection information** from the **automation tasks**, making it easier to reuse the same inventory with multiple playbooks.
