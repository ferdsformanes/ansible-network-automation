# Ansible Variables Explained | Cisco IOS Playbook for Beginners

A simple beginner-friendly guide to using **Ansible variables** with Cisco IOS.

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
- name: Run a Cisco IOS command using a variable
  hosts: cisco_ios
  gather_facts: false

  vars:
    cli_command: show clock

  tasks:
    - name: Run CLI command
      cisco.ios.ios_command:
        commands:
          - "{{ cli_command }}"
      register: output

    - name: Display command output
      ansible.builtin.debug:
        var: output.stdout
```

---

## 3. What Is an Ansible Variable?

A variable is a name that stores a value.

For example:

```yaml
cli_command: show clock
```

Here:

- `cli_command` = variable name
- `show clock` = variable value

Think of it like this:

```text
cli_command
     |
     v
show clock
```

Instead of writing `show clock` directly inside the task, we reference the variable:

```yaml
"{{ cli_command }}"
```

Ansible replaces the variable with its value when the playbook runs.

---

## 4. The `vars` Section

The `vars` section defines variables for the play:

```yaml
vars:
  cli_command: show clock
```

The variable can then be used by tasks in this play.

For example:

```yaml
commands:
  - "{{ cli_command }}"
```

Ansible sees:

```text
{{ cli_command }}
```

and replaces it with:

```text
show clock
```

The Cisco device therefore receives:

```text
show clock
```

---

## 5. Why Use Variables?

Without a variable, you could write:

```yaml
commands:
  - show clock
```

With a variable:

```yaml
vars:
  cli_command: show clock
```

and:

```yaml
commands:
  - "{{ cli_command }}"
```

The variable makes the playbook easier to reuse.

For example, change:

```yaml
cli_command: show clock
```

to:

```yaml
cli_command: show version
```

The task does not need to change.

---

## 6. Why Are Double Curly Braces Used?

Ansible uses **Jinja2 expressions** to reference variables.

For example:

```yaml
{{ cli_command }}
```

The double curly braces tell Ansible:

> Get the value of this variable.

So:

```yaml
{{ cli_command }}
```

becomes:

```text
show clock
```

when the playbook runs.

---

## 7. Run the Playbook

Run:

```bash
ansible-playbook -i inventory.yml variables.yml
```

Ansible connects to the devices in the `cisco_ios` group and runs:

```text
show clock
```

You should see the command output in the terminal.

---

## 8. Change the Variable

Try changing:

```yaml
vars:
  cli_command: show clock
```

to:

```yaml
vars:
  cli_command: show ip interface brief
```

Run the playbook again:

```bash
ansible-playbook -i inventory.yml variables.yml
```

The task itself did not change.

Only the variable changed.

This is one of the main benefits of variables.

---

## 9. Using an Extra Variable

You can also provide a variable from the command line.

For example:

```bash
ansible-playbook -i inventory.yml variables.yml -e "cli_command=show version"
```

The `-e` option means **extra variables**.

This allows you to change the command without editing the playbook.

For example:

```bash
ansible-playbook -i inventory.yml variables.yml -e "cli_command=show ip interface brief"
```

---

## 10. Understanding `register`

The playbook also contains:

```yaml
register: output
```

This saves the result of the task into a variable called `output`.

Then the next task uses:

```yaml
ansible.builtin.debug:
  var: output.stdout
```

So there are actually two variables in the playbook:

```yaml
cli_command
```

Stores the command we want to run.

And:

```yaml
output
```

Stores the result returned by the command.

---

## 11. How Everything Works

The overall flow is:

```text
inventory.yml
     |
     | Defines Cisco devices
     v
variables.yml
     |
     | cli_command = show clock
     v
{{ cli_command }}
     |
     | Becomes "show clock"
     v
cisco.ios.ios_command
     |
     | SSH
     v
Cisco IOS device
     |
     | Returns output
     v
register: output
     |
     v
output.stdout
     |
     v
Display output
```

---

## 12. Key Takeaways

### Variable

A variable stores a value:

```yaml
cli_command: show clock
```

### Variable reference

Use double curly braces to reference a variable:

```yaml
"{{ cli_command }}"
```

### `vars`

Defines variables for the play:

```yaml
vars:
  cli_command: show clock
```

### `register`

Stores the result of a task:

```yaml
register: output
```

### `debug`

Displays information:

```yaml
ansible.builtin.debug:
  var: output.stdout
```

---

## 13. Complete Example

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
- name: Run a Cisco IOS command using a variable
  hosts: cisco_ios
  gather_facts: false

  vars:
    cli_command: show clock

  tasks:
    - name: Run CLI command
      cisco.ios.ios_command:
        commands:
          - "{{ cli_command }}"
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

## What's Next?

A natural next step is using a **list variable** to store multiple Cisco commands:

```yaml
vars:
  cli_commands:
    - show clock
    - show version
    - show ip interface brief
```

This lets you run several commands while keeping the playbook simple.
