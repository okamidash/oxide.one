# Section 2 | OS Level Setup

This section covers os level setup for both virtual machines and physical machines. Generally I keep stuff at the OS defaults, with the following changes made.

## 02.01 - Operating System

The base operating system shall be based on Fedora 31 unless there is a specific reason not to. This is to make system management easier across the estate.

Physical hosts use Fedora server

Virtual hosts use Fedora cloud images.

## 02.02 - Packages

### DNF Automatic

To make package management easier, all hosts shall have dnf automatic configured and installed. 

Physical hosts shall have dnf-automatic configured to download only. Installing the packages is left up to the operator to do; which is completed on a weekly basis. 

This is done so that bringing down a physical host (and the vms that live on it) is planned, rather than sporadic.

Virtual hosts should have dnf-automatic configured to download and install immeadietely. Tihs is done because bringing down a vm won't mean bringing down an entire stack (usually).

**Setup**

`sudo dnf install -y dnf-automatic`

**For physical hosts**

`sudo systemctl enable dnf-automatic-download.timer && sudo systemctl start dnf-automatic-download.timer`

**Virtual Hosts**

`sudo systemctl enable dnf-automatic-install.timer && sudo systemctl start dnf-automatic-install.timer`

### Users

**okami**

okami is the default user setup for all systems. Every VM and Physical host should have this user existing by way of FreeIPA (LDAP).

They belong in the following groups (if they exist):

- docker

- wheel

- libvirt

### Sudo

Users in the wheel group shall have passwordless sudo enabled. This can be achieved with the following line.

` %wheel ALL=(ALL) NOPASSWD: ALL`

The following settings shall also be set in the sudoers file.

```
 Defaults env_keep += "LANG LANGUAGE LINGUAS LC_* _XKB_CHARSET"
 Defaults env_keep += "HOME"
 Defaults env_keep += "XAPPLRESDIR XFILESEARCHPATH XUSERFILESEARCHPATH"
 Defaults env_keep += "QTDIR KDEDIR"
 Defaults env_keep += "XDG_SESSION_COOKIE"
```

### SSH

SSH is the primary method of accessing any system across the estate.

Password authentication is not allowed anywhere.

`PasswordAuthentication no`

### LDAP

LDAP Through FreeIPA is to be setup and configured for each client on the network. This is to allow for easy user management across systems. This is setup using the FreeIPA ansible roles; and the home dir shall be set on creation.

MOTD
