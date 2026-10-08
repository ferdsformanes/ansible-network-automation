Ansible Dictionaries Explained | Cisco IOS Playbook for Beginners

Introduction

Ansible dictionaries let you store related information as key-value pairs.

For example, instead of storing separate variables for a Cisco device’s hostname and IP address, you can group them together in a dictionary.

This guide shows a simple dictionary example using a Cisco IOS playbook.

────────

1. What Is a Dictionary?

A dictionary stores data using keys and values.

Example:

device:
  hostname: R1
  ip_address: 192.168.100.24

Here:

• device is the variable.
• hostname is a key.
• R1 is the value.
• ip_address is another key.
• 192.168.100.24 is its value.

You can think of a dictionary as a container for related information.

────────

2. Create a Dictionary in Ansible

Here is a simple playbook:

---
- name: Learn Ansible dictionaries
  hosts: cisco_ios
  gather_facts: false

  vars:
    device:
      hostname: R1
      ip_address: 192.168.100.24

  tasks:
    - name: Display device information
      ansible.builtin.debug:
        msg: "Device {{ device.hostname }} has IP address {{ device.ip_address }}"

The dictionary is created under the vars section:

device:
  hostname: R1
  ip_address: 192.168.100.24

────────

3. Access Dictionary Values

You can access a value using dot notation:

{{ device.hostname }}

and:

{{ device.ip_address }}

For example:

msg: "Device {{ device.hostname }} has IP address {{ device.ip_address }}"

Ansible replaces the variables with their values when the playbook runs.

The output will look similar to:

Device R1 has IP address 192.168.100.24

────────

4. Dictionary with Cisco IOS Commands

Dictionaries become especially useful when you want to group information about commands.

Example:

---
- name: Run Cisco IOS commands using a dictionary
  hosts: cisco_ios
  gather_facts: false

  vars:
    commands:
      first: show clock
      second: show ip interface brief

  tasks:
    - name: Run Cisco IOS commands
      cisco.ios.ios_command:
        commands:
          - "{{ commands.first }}"
          - "{{ commands.second }}"
      register: output

    - name: Display command output
      ansible.builtin.debug:
        var: output.stdout_lines

The dictionary is:

commands:
  first: show clock
  second: show ip interface brief

The values are accessed with:

{{ commands.first }}

and:

{{ commands.second }}

────────

5. Dictionary vs List

A list stores multiple values in order:

cli_commands:
  - show clock
  - show ip interface brief

A dictionary stores values using keys:

cli_commands:
  clock: show clock
  interfaces: show ip interface brief

Think of it this way:

List:

Item 1
Item 2
Item 3

Dictionary:

key → value
key → value
key → value

Lists are useful when you simply need a collection of values.

Dictionaries are useful when each value has a meaningful name.

────────

6. A More Practical Cisco Example

You can use a dictionary to store information about a device:

---
- name: Display Cisco device information
  hosts: cisco_ios
  gather_facts: false

  vars:
    device:
      hostname: R1
      location: Manila
      role: Router

  tasks:
    - name: Display device information
      ansible.builtin.debug:
        msg:
          - "Hostname: {{ device.hostname }}"
          - "Location: {{ device.location }}"
          - "Role: {{ device.role }}"

This keeps related information together inside the device dictionary.

────────

7. Dictionary Keys Can Be Used in Tasks

You can also use dictionary values as parameters.

For example:

vars:
  device:
    hostname: R1
    command: show ip interface brief

tasks:
  - name: Run command
    cisco.ios.ios_command:
      commands:
        - "{{ device.command }}"

This makes the playbook easier to modify because the command is stored in one place.

────────

8. Important Syntax

Pay attention to the indentation:

vars:
  device:
    hostname: R1
    ip_address: 192.168.100.24

The indentation shows that hostname and ip_address belong to the device dictionary.

You can also use quotes when appropriate:

hostname: "R1"
ip_address: "192.168.100.24"

For simple values, quotes are often optional.

────────

9. Complete Example

Here is a beginner-friendly Cisco IOS example:

---
- name: Run Cisco IOS command using a dictionary
  hosts: cisco_ios
  gather_facts: false

  vars:
    device:
      hostname: R1
      command: show ip interface brief

  tasks:
    - name: Run command
      cisco.ios.ios_command:
        commands:
          - "{{ device.command }}"
      register: output

    - name: Display command output
      ansible.builtin.debug:
        var: output.stdout_lines

What happens?

1. Ansible creates the device dictionary.
2. The command key contains show ip interface brief.
3. {{ device.command }} retrieves that value.
4. ios_command sends the command to the Cisco IOS device.
5. The output is stored in output.
6. stdout_lines displays the command output in a readable format.

────────

10. Key Takeaways

• A dictionary stores key-value pairs.
• Dictionaries are useful for grouping related information.
• Use dot notation to access dictionary values:

{{ device.hostname }}

• Lists store values in order.
• Dictionaries give values meaningful keys.
• Dictionaries can make Cisco automation playbooks easier to organize and maintain.

Next Step

After learning dictionaries, a good next topic is Ansible Loops. Loops allow you to repeat tasks, which is especially useful when working with multiple commands or multiple network devices.