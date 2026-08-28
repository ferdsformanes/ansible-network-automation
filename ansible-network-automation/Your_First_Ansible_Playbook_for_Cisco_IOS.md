# Your First Ansible Playbook for Cisco IOS

A simple guide to creating and running your first Ansible playbook against a Cisco IOS device.

## 1. Create the Inventory

Create a file called:

```text
inventory.ini
```

Add your Cisco router:

```ini
[routers]
192.168.100.24
```

## 2. Create the Playbook

Create a file called:

```text
show_interfaces.yml
```

Add:

```yaml
---
- name: Show Cisco IOS Interface Status
  hosts: routers
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
ansible-playbook -i inventory.ini show_interfaces.yml -u cisco -k -c network_cli -e "ansible_network_os=cisco.ios.ios"
```

Enter the Cisco device password when prompted.

## 4. Understand the Playbook

* `hosts: routers` — runs the playbook against the `routers` group.
* `gather_facts: false` — skips automatic fact gathering.
* `cisco.ios.ios_command` — runs commands on Cisco IOS.
* `show ip interface brief` — displays interface status and IP addresses.
* `register: output` — saves the command result.
* `debug` — displays the result in the terminal.

## 5. Expected Result

You should see output similar to:

```text
GigabitEthernet0/0    192.168.1.1    YES    manual    up    up
GigabitEthernet0/1    unassigned    YES    unset     down  down
```

## Summary

You have now created and executed your first Ansible playbook for Cisco IOS.

The basic workflow is:

```text
Inventory → Playbook → Task → Cisco IOS Command → Output
```
