---
icon: lucide/rocket
---

# Getting Started

This Article gives some general guidance on how to get started contributing with the Infrastructure
or mesh networks in general.

As a general requirement, you should have a basic (end-user) knowledge of networking and Linux.
You don't have to be seasoned network engineer but should be able to boot a Linux system, open a terminal
window and connect to a WiFi network.


!!! note

	If you are new to Linux, we recommend to start with a Linux distribution like Ubuntu or Linux Mint.
	They provide a good user experience and support installing the Nix package manager required for our
	infrastructure.


!!! warning

	While generally possible, we do not recommend using a Virtual Machine (VM). You will encounter
	situations sooner or later where it is advantageous being able to use your wireless NIC with
	Linux directly. This is not possible in a VM and will limit your ability.


## Generating an SSH Key

A SSH key is required to connect to a remote Linux system securely. It consists of a
public and a private key.
The public key is shared with the remote system, while the private key is kept secret on
your local machine and is used to authenticate your identity.

A SSH key can be generated using the `ssh-keygen` command. The following command will generate a new SSH key pair:

```bash
ssh-keygen -t ed25519 -f ~/.ssh/id_ed25519
```

You can find the public key in the `~/.ssh/id_ed25519.pub` file and the private key in the `~/.ssh/id_ed25519` file.


## Connecting to a Remote System

Connecting to a remote system can be done using the `ssh` command.

The following command will connect to the remote system at `gw1.as62028.de` using the user `root`.

```bash
ssh root@gw1.as62028.de
```

Ending an SSH session can be done by typing `exit` or pressing `Ctrl+D`.

The following use cases can be used to connect to a remote system:

### Forwarding a Remote Port to the Local System

```bash
ssh -L 8080:localhost:80 root@gw1.as62028.de
```

This command forwards the remote port 80 to the local port 8080. You
can then access the remote service by opening `http://localhost:8080` in your web browser.

This can be useful for accessing web interfaces of services running on the remote
system that are not exposed to the public internet.


!!! info 

	You can also replace `localhost` with the IP address of another system that is accessible from the remote system. This allows you to access services running on other systems in the same network as the remote system.

### Using a Jump Host

```bash
ssh -J root@router.as62028.de root@gw1.as62028.de
```

Using a jump host allows you to connect to a remote system through an intermediate system.
This can be useful for accessing systems that are not directly accessible from your local machine.
