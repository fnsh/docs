---
icon: lucide/rocket
---

# Firmware Build Process

Our Firmware is build using the Gluon framework. Gluon is based on OpenWrt with a Focus on Mesh-Networks.


## Build Yourself

There are two ways of building Firmware to use with our network. You either clone our site into a pre-existing Gluon
tree or use our provided build-scripts within the site repo.

The difference between these methods is that using a vanilla build-tree does not include custom patches we ship with our site repository. This method is generally used when developing Gluon firmware. Out build-scripts also reference a specific
Gluon version to match releases built using our CI.

### Vanilla build-tree

#### Preperation

To build using an existing Gluon tree, you first need to prepare a Gluon Tree yourself.

Clone the current Gluon Master branch from the official repository:

```bash
git clone https://github.com/freifunk-gluon/gluon.git
```

When the repository is cloned, you need to clone the site directory.

```bash
cd gluon
git clone https://github.com/fnsh/site.git site
```

The resulting directory structure should look like this:

```
gluon/
├── site/
└── ...
```

#### Update feeds

Prepare the required packages feed by updating and installing the feeds.
Perform this inside the `gluon` directory.

```bash
make update
```

!!! tip

    As sites specify their own additional set of package feeds in the `modules` file, this needs to be done
	everytime the site or site version has changed.
	
	Gluon generally does not warn if a mismatch between the checked-out site modules and the defined modules exist.


#### Start Build

After the feeds have been updated, the build can be started using:

```bash
make GLUON_TARGET=ramips-mt7621 -j16 V=s
```

A Gluon build is always per-target. The previously mentioned command builds the `ramips-mt7621` target with `16` parallel jobs and verbose output.

A list of supported targets can be found in the Gluon documentation or by running:

```bash
make list-targets
```

!!! warning

    The Gluon version is encoded in the build-artifact. To have a working upstream version reference, ensure
	the latest git tags are pulled from upstream.

	To update the local repository with the latest tags, run:

	```bash
	git fetch --tags https://github.com/freifunk-gluon/gluon.git
	```


### Using Build-Scripts

Our site repository provides build-scripts that automate the process of preparing the Gluon
tree and building the firmware. This method ensures that the correct Gluon version and patches are applied.

To use the build-scripts, first clone our site repository if you haven't already:

```bash
git clone https://github.com/fnsh/site.git site
cd site
```

Clone Gluon and prepare the build environment using the provided build-scripts:

```bash
make update
```

Enter the `gluon` directory:

```bash
cd gluon
```

From there, continue the build process as described in the "Vanilla build-tree" section,
starting with updating the feeds and then building the desired target.
