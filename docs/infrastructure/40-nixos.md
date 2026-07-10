---
icon: fontawesome/brands/linux
---

# NixOS
NixOS is a declarative Linux distribution. That means that all parts of the system are declared up front in a well-defined form, usually in a git repo. Our NixOS configuration lives here (TODO: insert link here). 

## Configuring NixOS host
Usually in the *nix world you install a System, make some changes to the configuration and then preserve the systems configuration through backups (or not at all). NixOS forces you to declare your configuration up front and then apply (or deploy) this configuration onto a system. When you apply the same configuration twice, you will get the same result both times.

## Deploying NixOS configurations
There are a lot of different tools / methods to deploy NixOS configurations. We are using colmena with nix flakes. For detailed instructions see (TODO: insert link to nixos repo).

## What is a flake
Essentially a `flake.nix` is some boilerplate code that serves as an entrypoint to whatever you are trying to achieve with your nix code. Part of the flake ecosystem is also the `flake.lock` which ,like many other package managers, locks your dependancies to a specific version.

!!! warning

	Because the flake also considers your local git repo one of its inputs, **any files you have that are not tracked by git will be ignored when using the flake**.


## Lifecycle of a NixOS system

This is a description of how we use NixOS. Some of this is very opinionated towards our specific setup.

### Installing a new VM

#### Creating the install ISO
We have a custom installer ISO, which you can build by running `nix build .#installer` in the Nixos repo (TODO: link). If all went well you should see a `result` folder with the custom iso in the `iso` folder. This folder is actually a symlink into the read-only nix store, if you want to delete the symlink you need to use `rm result` **without a trailing slash**.

This custom ISO should then be uploaded into the proxmox host.

TODO: explain how to create a new VM in terraform? Link to somewhere else?

#### Booting the VM
By default a freshly created VM will boot, but not start into an operating system (because none is installed yet). You need to stop the machine, go into the Hardware settings and add a CD Drive, where you  select the uploaded ISO. Then you can start the VM. We use uefi / q35 for all our VMs.


#### Accessing the installer
The custom installer will find the management network interface (if one is configured) and configure the special IP `172.20.210.242` and a default route on the interface. This means that you can access the system via this address over ssh. As with the other VMs, the ssh keys are added as authorized for the root user.

!!! note

	If you start the installer image two times, this **will** lead to address conflicts in the management network.

For ssh access to the installer, we recommend setting the following settings in your `~/.ssh/config`:

```ssh_config
Host 172.20.210.242 fnsh-install
	# Ignore host keys because they are not preserved in the installer
	StrictHostKeyChecking no
	UserKnownHostsFile=/dev/null

	IdentityFile ~/.ssh/fnsh # Replace this path with the path to your specific ssh key
	User root
	HostName 172.20.210.242
```

Now you should be able to connect to the installer, either via `ssh root@172.20.210.242` or just `ssh ffda-install`.

#### Installing the system
Along with the new VM, you should have created a new system configuration in the colmena hive. You can use the provided `install.sh` script to install this configuration onto the VM. The script takes two parameters, the first is the name of the system configuration you created in the colmena hive (e.g. `router1`). The second parameter is the destination used by ssh. You can use `fnsh-install` if you have configured your `~/.ssh/config` as described above. When you run the script, nix will copy the built system closure from you local machine to the installer, and install nixos. The partitioning is done automatically using disko, since the partition scheme is the same for all hosts. At the end of the script the VM should reboot into the system configuration. At this point you can go into the Hardware options and remove the CD drive.

### Making changes to the system

When you want to change something about a system, you need to change it in the git repo. Then you can deploy your changes using `colmena apply --on <system>` where `<system>` can be the name of a specific configuration, a tag (e.g. `@gw` for all gateways) or a comma-separated list of either. Example to push the configuration to the router and all gateways: `colmena apply --on router1,@gw`.


### Updating the system
To update the nixos system, run `nix flake update` to update the inputs of the flake. Most importantly this updates nixpkgs, which is the source of most things in NixOS. Then you can use the same method described above to deploy the new configurations onto the VMs. There is no way to update just a single VM, you can only update the inputs for all of them.

#### Stateversion
Each NixOS system has a configuration option called `stateVersion`. This option is used to remember which version of NixOS was first installed on a host, to prevent breaking changes on certain upgrades. For our use cases this is largely irrelevant.
