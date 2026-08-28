# Run Your First Ansible Ad Hoc Command | Cisco Network Automation

## 1. Prerequisites

Before starting, make sure you have:

- Ubuntu/WSL with Ansible installed
- A reachable Cisco IOS device
- The Cisco IOS collection installed
- SSH access to the Cisco device

Check Ansible:

```bash
ansible --version
```

Check the Cisco IOS collection:

```bash
ansible-galaxy collection list
```

If needed, install it:

```bash
ansible-galaxy collection install cisco.ios
```

## 2. Check Connectivity to the Cisco Device

Make sure your Cisco device is reachable.

Example:

```bash
ping 192.168.1.10
```

## 3. Run Your First Ad Hoc Command

Use the following command:

```bash
ansible all -i "192.168.1.10," -m cisco.ios.ios_command -a "commands='show ip int brief'" -u <username> -k -c network_cli -e "ansible_network_os=cisco.ios.ios"
```

Replace:

```text
<username>
```

with your Cisco username.

For example:

```bash
ansible all -i "192.168.1.10," -m cisco.ios.ios_command -a "commands='show ip int brief'" -u cisco -k -c network_cli -e "ansible_network_os=cisco.ios.ios"
```

## 4. Enter the Password

After running the command, Ansible will prompt you for the SSH password:

```text
SSH password:
```

Enter the password for your Cisco device.

## 5. Understand the Command

Here is what each part means:

| Option | Meaning |
|---|---|
| `ansible` | Runs an Ansible ad hoc command |
| `all` | Targets all hosts in the inventory |
| `-i "192.168.1.10,"` | Uses `192.168.1.10` as a one-host inventory |
| `-m cisco.ios.ios_command` | Uses the Cisco IOS command module |
| `-a "commands='show ip int brief'"` | Runs the Cisco command |
| `-u <username>` | Specifies the SSH username |
| `-k` | Prompts for the SSH password |
| `-c network_cli` | Uses Ansible's network CLI connection |
| `-e` | Defines an extra variable |
| `ansible_network_os=cisco.ios.ios` | Specifies Cisco IOS as the network OS |

### Important: The comma after the IP address

Notice this:

```bash
-i "192.168.1.10,"
```

The comma tells Ansible that `192.168.1.10` is a host in an inline inventory.

Without the comma, Ansible may treat `192.168.1.10` as an inventory file or path.

## 6. Check the Output

If the connection and command are successful, Ansible will return the output of:

```text
show ip int brief
```

You should see information similar to:

```text
R1 | SUCCESS => {
    "stdout": [
        "Interface              IP-Address      OK? Method Status                Protocol"
    ]
}
```

The exact output will depend on your Cisco device.

## 7. What You Just Did

You used Ansible to:

1. Connect to a Cisco IOS device.
2. Authenticate using SSH.
3. Use the `cisco.ios.ios_command` module.
4. Execute `show ip int brief`.
5. Return the command output.

You did all of this **without creating an inventory file or playbook**.

## Quick Reference

```bash
ansible all -i "192.168.1.10," -m cisco.ios.ios_command -a "commands='show ip int brief'" -u <username> -k -c network_cli -e "ansible_network_os=cisco.ios.ios"
```

**Next step:** Try running another Cisco command such as:

```text
show version
```
