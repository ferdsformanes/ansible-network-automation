# Ansible Variables Explained | Run Multiple Cisco IOS Commands

A simple beginner-friendly guide to using **Ansible variables** to run multiple Cisco IOS commands.

This guide uses the same YAML inventory from the previous Ansible lessons.

---

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

### What this inventory does

- `R1` and `S1` are the Cisco devices.
- `ansible_host` contains the IP address of each device.
- `ansible_connection` tells Ansible to use the network CLI connection.
- `ansible_network_os` tells Ansible that the devices use Cisco IOS.
- `ansible_user` specifies the SSH username.

---

## 2. Create the Playbook

Create a file called:

```text
variables.yml
```

Add:

```yaml
---
- name: Run Cisco IOS commands using a variable
  hosts: cisco_ios
  gather_facts: false

  # Variables store values that can be reused in the playbook
  vars:
    cli_commands:
      - show clock
      - show ip interface brief

  tasks:
    - name: Run CLI commands
      cisco.ios.ios_command:
        commands: "{{ cli_commands }}"
      register: output

    - name: Display command output
      ansible.builtin.debug:
        var: output.stdout_lines
```

---

## 3. What Is an Ansible Variable?

A variable is a name that stores a value.

For example:

```yaml
cli_commands:
  - show clock
  - show ip interface brief
```

Here:

```text
cli_commands
```

is the **variable name**.

The values stored in the variable are:

```text
show clock
show ip interface brief
```

In this example, the variable stores a **list of commands**.

Think of it like this:

```text
cli_commands
     |
     +-- show clock
     |
     +-- show ip interface brief
```

---

## 4. The `vars` Section

The `vars` section is where we define variables for the play:

```yaml
vars:
  cli_commands:
    - show clock
    - show ip interface brief
```

The variable is named:

```text
cli_commands
```

It contains two values.

The indentation and `-` characters indicate that these values belong to a YAML list.

---

## 5. What Is a List Variable?

A variable does not have to store just one value.

For example, a variable can store a list:

```yaml
cli_commands:
  - show clock
  - show ip interface brief
```

The `-` indicates each item in the list.

So this:

```yaml
cli_commands:
  - show clock
  - show ip interface brief
```

means:

```text
cli_commands = [
    "show clock",
    "show ip interface brief"
]
```

You can add more commands if needed:

```yaml
cli_commands:
  - show clock
  - show version
  - show ip interface brief
```

---

## 6. Referencing the Variable

The playbook references the variable using:

```yaml
"{{ cli_commands }}"
```

For example:

```yaml
commands: "{{ cli_commands }}"
```

The double curly braces tell Ansible to use the value of the variable.

Ansible replaces:

```yaml
"{{ cli_commands }}"
```

with the list stored in:

```yaml
cli_commands:
  - show clock
  - show ip interface brief
```

The Cisco IOS module then runs both commands.

---

## 7. Running Multiple Commands

The `cisco.ios.ios_command` task is:

```yaml
- name: Run CLI commands
  cisco.ios.ios_command:
    commands: "{{ cli_commands }}"
```

The `commands` parameter expects a list of Cisco IOS commands.

Our variable provides that list:

```yaml
cli_commands:
  - show clock
  - show ip interface brief
```

This allows one task to run multiple commands.

Without a variable, you could write:

```yaml
- name: Run CLI commands
  cisco.ios.ios_command:
    commands:
      - show clock
      - show ip interface brief
```

Using a variable separates the **data** from the **task**.

---

## 8. Why Use Variables?

Variables make playbooks easier to reuse and modify.

For example, you can change:

```yaml
cli_commands:
  - show clock
  - show ip interface brief
```

to:

```yaml
cli_commands:
  - show clock
  - show version
  - show ip interface brief
```

The task does not need to change.

The task remains:

```yaml
- name: Run CLI commands
  cisco.ios.ios_command:
    commands: "{{ cli_commands }}"
```

Only the variable changes.

---

## 9. Add Another Command

Try adding:

```yaml
- show version
```

Your variable becomes:

```yaml
vars:
  cli_commands:
    - show clock
    - show version
    - show ip interface brief
```

Run the playbook again:

```bash
ansible-playbook -i inventory.yml variables.yml
```

Ansible will run all three commands on the Cisco devices.

---

## 10. Understanding `register`

The playbook contains:

```yaml
register: output
```

This saves the result of the `ios_command` task into a variable called:

```text
output
```

So there are two important variables in the playbook:

### `cli_commands`

Stores the commands we want to run:

```yaml
cli_commands:
  - show clock
  - show ip interface brief
```

### `output`

Stores the result returned by the task:

```yaml
register: output
```

---

## 11. Understanding `stdout_lines`

The final task is:

```yaml
- name: Display command output
  ansible.builtin.debug:
    var: output.stdout_lines
```

`output.stdout_lines` contains the command output organized into lines.

Using:

```yaml
output.stdout_lines
```

usually makes the output easier to read than displaying the raw `stdout` value.

For example, the result is organized roughly like:

```text
Command 1 output:
show clock
...

Command 2 output:
show ip interface brief
...
```

---

## 12. Why Use `stdout_lines` Instead of `stdout`?

You can display:

```yaml
var: output.stdout
```

but `stdout_lines` is often cleaner for CLI output.

For network automation, this is useful when you're working with commands such as:

```text
show clock
show version
show ip interface brief
```

and want the output displayed in a readable line-by-line format.

---

## 13. Run the Playbook

Run:

```bash
ansible-playbook -i inventory.yml variables.yml
```

Ansible will:

1. Read the inventory.
2. Find the devices in `cisco_ios`.
3. Read the `cli_commands` variable.
4. Run the commands using `cisco.ios.ios_command`.
5. Store the results in `output`.
6. Display `output.stdout_lines`.

---

## 14. How Everything Works

The overall flow is:

```text
inventory.yml
     |
     | Defines Cisco devices
     v
variables.yml
     |
     | cli_commands
     |
     +-- show clock
     |
     +-- show ip interface brief
     |
     v
{{ cli_commands }}
     |
     | Provides the list of commands
     v
cisco.ios.ios_command
     |
     | SSH
     v
Cisco IOS devices
     |
     | Returns command output
     v
register: output
     |
     v
output.stdout_lines
     |
     v
Display output
```

---

## 15. Complete Example

### inventory.yml

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

### variables.yml

```yaml
---
- name: Run Cisco IOS commands using a variable
  hosts: cisco_ios
  gather_facts: false

  # Variables store values that can be reused in the playbook
  vars:
    cli_commands:
      - show clock
      - show ip interface brief

  tasks:
    - name: Run CLI commands
      cisco.ios.ios_command:
        commands: "{{ cli_commands }}"
      register: output

    - name: Display command output
      ansible.builtin.debug:
        var: output.stdout_lines
```

### Run

```bash
ansible-playbook -i inventory.yml variables.yml
```

---

## 16. Key Takeaways

### Variable

A variable stores a value:

```yaml
cli_commands:
  - show clock
  - show ip interface brief
```

### List

The `-` creates items in a YAML list:

```yaml
- show clock
- show ip interface brief
```

### Variable reference

Use double curly braces to reference a variable:

```yaml
"{{ cli_commands }}"
```

### `vars`

Defines variables for the play:

```yaml
vars:
  cli_commands:
    - show clock
    - show ip interface brief
```

### `register`

Stores the result of a task:

```yaml
register: output
```

### `stdout_lines`

Displays the command output line by line:

```yaml
var: output.stdout_lines
```

---

## What's Next?

A natural next step is learning about **Ansible variable types**, such as strings, lists, and dictionaries.

You can then use more structured variables to make your network automation playbooks more flexible and reusable.
