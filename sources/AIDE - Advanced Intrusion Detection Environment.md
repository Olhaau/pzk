---
title: AIDE - Advanced Intrusion Detection Environment
---

# AIDE - Advanced Intrusion Detection Environment

url:: https://aide.github.io/
up: [[sources]]

#source

# AIDE

## About AIDE

AIDE (Advanced Intrusion Detection Environment, [eyd]) is a file and directory integrity checker.

## What does it do?

It creates a database from the regular expression rules that it finds from the config file(s). Once this database is initialized it can be used to verify the integrity of the files. It has several message digest algorithms (see below) that are used to check the integrity of the file. All of the usual file attributes can also be checked for inconsistencies. It can read databases from older or newer versions. See the manual pages within the distribution for further info.

## Features

* supported message digest algorithms: md5, sha1, rmd160, tiger, crc32, sha256, sha512, whirlpool (additionally with libmhash: gost, haval, crc32b)
* supported file attributes: File type, Permissions, Inode, Uid, Gid, Link name, Size, Block count, Number of links, Mtime, Ctime and Atime
* support for Posix ACL, SELinux, XAttrs and Extended file system attributes if support is compiled in
* plain text configuration files and database for simplicity
* powerful regular expression support to selectively include or exclude files and directories to be monitored
* gzip database compression if zlib support is compiled in
* stand alone static binary for easy client/server monitoring configurations
* and many more

## Get AIDE

The current
*stable*

version of AIDE is
**0.19**

### Downstreams

AIDE is included in the following distributions. Please use the corresponding command to install AIDE.

* [Debian GNU/Linux](https://tracker.debian.org/pkg/aide) | [Ubuntu](https://launchpad.net/ubuntu/+source/aide): `apt install aide`
* [FreeBSD](https://cgit.freebsd.org/ports/tree/security/aide/): `pkg install aide`
* [Gentoo](https://packages.gentoo.org/packages/app-forensics/aide): `emerge aide`
* [Homebrew](https://formulae.brew.sh/formula/aide): `brew install aide`
* [MacPorts](https://trac.macports.org/browser/trunk/dports/security/aide/Portfile): `port install aide`
* [NixOS](https://search.nixos.org/packages?show=aide&query=aide): `nix-env -iA nixos.aide`
* [OpenBSD](https://cvsweb.openbsd.org/cgi-bin/cvsweb/ports/security/aide/): `pkg_add aide`
* [openSUSE](https://software.opensuse.org/package/aide) | SUSE: `zypper install aide`
* Red Hat | CentOS | [Fedora](https://packages.fedoraproject.org/pkgs/aide/aide): `yum install aide`

See also the output of `whohas aide`.

Basically AIDE runs on any modern Unix.

Please report to the [aide.github.io issue tracker](https://github.com/aide/aide.github.io/issues) if your distribution is not listed above.

### Source

Both the source tarballs on GitHub and the tags of the git repository (since v0.16a1) are GnuPG-signed.

Since v0.16a2 the key used for signing is the GnuPG key of
*Hannes von Haugwitz*

(the current maintainer of AIDE).

The current public key can be downloaded from one of the well known PGP key servers.

The current key is:

```
pub   4096R/68E7B931 2011-06-28 [expires: 2025-06-27]
      Key fingerprint = 2BBB D30F AAB2 9B32 53BC  FBA6 F694 7DAB 68E7 B931
uid                  Hannes von Haugwitz <hannes@vonhaugwitz.com>
```

Please
**always**

verify the signature of a release before using it (see below).

#### Source tarballs

The source code of the latest stable release can be downloaded
[here](https://github.com/aide/aide/releases)

.

Use the following command to verify the signature of the downloaded source tarball (see
[README](https://github.com/aide/aide/blob/master/README)

file for details):

`gpg --verify aide-<VERSION_NUMBER>.tar.gz.asc`

#### GIT

The AIDE git repository can be found
[here](https://github.com/aide/aide)

.

Use the following command to verify the signature of a git tag (see
[README](https://github.com/aide/aide/blob/master/README)

file for details):

`git verify-tag v<VERSION_NUMBER>`

## License

AIDE is licensed under GPL-2.0.

## Get Help

### Mailing list

For questions about the usage of AIDE and announcements of final releases or other project news there is the AIDE
[mailing list](https://www.ipi.fi/mailman/listinfo/aide)

on ipi.fi (Thanks to
*Rami Lehti*

for maintaining it).

### IRC

There is a discussion channel
*#aide*

on
[irc.oftc.net](https://oftc.net/)

for questions about the usage and discussions about the development of AIDE.

### Issues, Feature requests and pull requests

Submit an [issue](https://github.com/aide/aide/issues) on GitHub

## Who's behind AIDE?

AIDE was originally written by
**Rami Lehti**

and
**Pablo Virolainen**

in 1999.

Between 2003 and 2010 it was maintained by
**Richard van den Berg**

.

In October 2010
**Hannes von Haugwitz**

took over the project.

For the mail addresses of the authors please see the
[AUTHORS](https://github.com/aide/aide/blob/master/AUTHORS)

file.
