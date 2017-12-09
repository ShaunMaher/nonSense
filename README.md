# nonSense

## Overview

This is not intended to be a fork of pfSense.  I love pfSense.  I just want to
do some experiments with pfSense (that will likely fail) and in order to do so I
need to be able to build it from source.

The pfSense license allows us to build from it's source, we're just not allowed
to call what we build "pfSense".  To facilitate this the pfSense build scripts
want to call what they build "nonSense".  This complicates things a little
because the builder is going to want to find files with "nonSense" in their
names and download files with "nonSense" in the URLs.

What I have here is the source pfSense 2.4.2 with some slight modifications so
that it can actually build "nonSense".  I'll also document the changes made so
that others can replicate (and improve) them.  See the file
"Building_nonSense.md" for more information.

You will not find nonSense or pfSense binaries or images here.  This is entirely
for people that are interested in building pfSense from source for whatever
reason.

## Work in progress

This is still a work in progress.  Hopefully I'll have a working modified source
tree here soon but right now, the only change committed is this README file.

# pfSense

## Overview

The pfSense project is a free network firewall distribution, based on the FreeBSD operating system with a custom kernel and including third party free software packages for additional functionality. pfSense software, with the help of the package system, is able to provide the same functionality or more of common commercial firewalls, without any of the artificial limitations. It has successfully replaced every big name commercial firewall you can imagine in numerous installations around the world, including Check Point, Cisco PIX, Cisco ASA, Juniper, Sonicwall, Netgear, Watchguard, Astaro, and more.

pfSense software includes a web interface for the configuration of all included components. There is no need for any UNIX knowledge, no need to use the command line for anything, and no need to ever manually edit any rule sets. Users familiar with commercial firewalls catch on to the web interface quickly, though there can be a learning curve for users not familiar with commercial-grade firewalls.

pfSense started in 2004 as a fork of the [m0n0wall](http://m0n0.ch/wall/index.php "m0n0wall project homepage") Project (which ended 2015/02/15), though has diverged significantly since.

pfSense is Copyright 2004-2016 [Rubicon Communications, LLC (Netgate)](https://pfsense.org/license "License Information") and published under an open source license.
Read more at [https://pfsense.org/](https://pfsense.org/ "The pfSense homepage") and support the team by buying a Gold Membership Subscription, bundled hardware appliances or commercial support.

## Contribute

The pfSense project welcomes contributions, big or small. Members of the pfSense community frequently contribute bug fixes, enhancements, and documentation changes to improve the functionality and usability of the software.

All of the pfSense project source code is on Github. We recommend potential contributors familiarize themselves with [the pfSense project git repositories](https://github.com/pfsense) and [Github in general](https://help.github.com).

If you want to contribute but do not have a specific topic in mind, review the [list of open bug reports and other issues that are in need of attention](https://redmine.pfsense.org/projects/pfsense/issues).

Before making source code changes, be sure to review our [Developer Style Guide](https://doc.pfsense.org/index.php/Developer_Style_Guide) so that the submitted code matches our preferred coding style.

Changes may be submitted as [pull requests on github](https://help.github.com/articles/using-pull-requests/) once the LA and CLA signing process has been completed. After being submitted, our developers will review the requests, offer feedback, and merge the changes if they are acceptable.

Contact [coreteam@pfsense.org](mailto:coreteam@pfsense.org "Mail to coreteam@pfsense.org") with any additional questions or concerns.
