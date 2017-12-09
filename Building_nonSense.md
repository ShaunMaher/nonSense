## Before you begin
### License (at least as I understand it)
The pfSense license is an open source license.  You can take the source and do
with it as you will.  The main thing you're NOT allowed to do is to call you're
resulting software pfSense.  For this reason, to source you download will, by
default want to call itself nonSense.

There are ways to change this so it goes back to calling itself pfSense,
afterall, we have the source of the build system that enforces this, but at this
point let's just go with the flow and call our resulting software nonSense.

If we get to a point where we have something useful we might look at coming up
with a proper name and branding or upstream our work into the real pfSense.

## Preperation
### FreeBSD VM
pfSense/nonSense needs to be built on FreeBSD.  If you don't have a handy
FreeBSD host, download the FreeBSD (I used the latest stable version)
installation .ISO, spool up and VM and install.

I think I used the default settings all the way through the installation wizard.

### Useful and required packages
```
pkg install sudo vim-lite tmux git bash
```

### Stop sendmail starting at boot because it takes too long and we don't need it
```
vim /etc/rc.conf
```
append the following:
```
sendmail_enable="NONE"
sendmail_msp_queue_enable="NO"
sendmail_outbound_enable="NO"
sendmail_submit_enable="NO"
```

## Download sources
We're going to keep two copies of the source tree, one will be untouched, the
other will get our modifications.  Then we can use `diff` to create a patch set.

For the original upstream pfSense source:
```
cd /var
mkdir nonSense.orig
cd nonSense.orig
git clone https://github.com/pfsense/pfsense.git .
cd ..
cp -a nonSense.orig nonSense
```

For my pre-modified source:
```
cd /var
mkdir nonSense.orig
cd nonSense.orig
git clone https://github.com/ShaunMaher/nonSense.git .
cd ..
cp -a nonSense.orig nonSense
```

## NonSense: Missing parts
Although the build system wants to create a product called nonSense by default,
it's still not really configured or coded properly to support this
out-of-the-box (which is fair enough really).

We are going to plug the gaps, mostly by just copying files called pfSense* to
nonSense*

**Note:** If you are using the https://github.com/ShaunMaher/nonSense.git
repository, all of these changes and fixes have already been made for you.  Skip
ahead to the "Create build.conf" section.

### Configuration files
We need to copy/rename some pfSense files to nonSense.
```
cd /var/nonSense/tools/templates/pkg_repos
cp pfSense-repo.conf nonSense-repo.conf
cp pfSense-repo.descr nonSense-repo.descr
cp pfSense-repo-devel.conf nonSense-repo-devel.conf
cp pfSense-repo-devel.descr nonSense-repo-devel.descr
```
TODO: If we intend to use the pfSense pkg repositories we need to edit the above
files slightly.  They contain URL templates that will be translated to nonSense
instead of pfSense.

In the nonSense*.conf files, on the "url:" and "fingerprints:" lines only,
replace "%%PRODUCT_NAME%%" with "pfSense".  Still in the nonSense*.conf files,
set "signature_type" to "none" and remove the "fingerprints:" lines.  This isn't
ideal but we can circle back anbd find a better solution later.

If we don't want to use the pfSense pkg repositories, we need to make changes to
tools/build_defaults.sh to remove the pfSense/NetGate URL prefixes.

### Modify builder scripts very slightly
I have created some modifications for the provided building scripts.  Some are
ugly hacks, some are less ugly hacks.

In a few places I add the idea of a "parent" product (which is configured in
build.conf).  In the places that matter I add a check to see if PRODUCT_NAME
(in our case "nonSense") is a viable option (e.g. a file exists with that name)
and if it is not, I substitute PARENT_PRODUCT_NAME.  For example, one script
wants to install a package called "${PRODUCT_NAME}-builder".  We haven't created
our own "nonSense-builder", we're happy to use "pfSense-builder".  My
modification to the script checks to see if a package called
${PRODUCT_NAME}-builder ("nonSense-builder") exists.  When it finds that it does
not, it installs ${PARENT_PRODUCT_NAME}-builder ("pfSense-builder") instead.

FreeBSD-src is downloaded from git before building and any local changes are
removed.  For this reason the builder_common.sh script is modified to look for
some ${PRODUCT_NAME} files and copy ${PARENT_PRODUCT_NAME} files if they are
missing.  Specifically:
* release/conf/${PRODUCT_NAME}_make.conf
* release/conf/${PRODUCT_NAME}_src.conf
* release/conf/${PRODUCT_NAME}_src-env.conf
* sys/amd64/conf/${PRODUCT_NAME}

Part of the process (install_pkg_install_ports) wants to install a package
called ${PRODUCT_NAME}.  I haven't worked out how to change the name of the
package that is created to ${PACKAGE_NAME} so I have just hard coded "pfSense"
on line 363 of build.sh
```
install_pkg_install_ports pfSense
```
This solution needs improvement.

The pfSense package is defined in the FreeBSD ports collection:
https://github.com/pfsense/FreeBSD-ports/blob/devel/security/pfSense/

pfSense-builder is also in the ports collection:
https://github.com/pfsense/FreeBSD-ports/tree/devel/sysutils/pfSense-builder/

### Create build.conf
You can either create a build.conf file based on build.conf.sample:
```
cp build.conf.sample build.conf
```
or use the build.conf.nonSense I have created for you:
```
ln -s build.conf.nonSense build.conf
```

## Cross Compiling: TODO
Supporting architectures that pfSense themselves don't support such as Arm or
i386?  Are we mad?  Probably.

TODO: TARGET and TARGET_ARCH are pulled from uname in tools/builder_defaults.sh
Can we override in build.conf?  build.sh suggests so:
```
# Let user define ARCH_LIST in build.conf
[ -z "${ARCH_LIST}" -a -n "${DEFAULT_ARCH_LIST}" ] \
	&& ARCH_LIST="${DEFAULT_ARCH_LIST}"
```

FreeBSD-src/sys/arm*/conf/pfSense is missing?  Can we copy the amd64 one?

FreeBSD-src/sys/i386/conf/pfSense is missing?  Can we copy the amd64 one?

## Build
```
cd /var/nonSense
rm /usr/local/etc/pkg/repos/nonSense.conf
./build.sh iso
```

## Build errors
### Trying to download files from release-staging.netgate.com even after you know you have edited the config correctly
This is because the file /usr/local/etc/pkg/repos/nonSense.conf isn't
regenerated if it's been generated once (i.e. before you made the correct
edits).  Delete the file and it will be recreated:
```
rm /usr/local/etc/pkg/repos/nonSense.conf
```

## Notes
### pkg.pfsense.org is not the DNS name of a server
This DNS name appears in several places but it's not a normal A/CNAME record
pointing to a web server as you mayt expect.  It's actually an SRV record:
```
host -t srv _https._tcp.pkg.pfsense.org
_https._tcp.pkg.pfsense.org has SRV record 10 10 443 files01.netgate.com.
_https._tcp.pkg.pfsense.org has SRV record 10 10 443 files00.netgate.com.
```
