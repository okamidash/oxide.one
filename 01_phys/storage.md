# Filesystem Layout

For all machines, physical and virtual; storage specific directories are kept in the `/str` directory. The purpose of this is to make it clear the type of storage, where it is and to root FS uncluttered.

The following directories are to be made when a system is provisioned.

`/str/loc`

`/str/net`

## Network Storage

Network based storage is kept under `/str/net`

The pattern followed for network storage is as follows:

`/str/net/{type}/{host}/{volume}`

Where `type` is the method of storage, abbreviated to 3 letters. It is good practice to keep the first character unique, so that tab completion is easier.

- Network FIle System (NFS): `/str/net/nfs`

- Gluster FS: `/str/net/gfs`

- Ceph Storage: `/str/net/cep`

`host` relates to where the storage is kept. For a NFS based storage exported on the host 'blackbird' the directory would be `/str/net/nfs/blackbird/`

`volume` relates to the name of the share/volume exported.  Continuing the previous example, for a share called `cloudinit` the full storage path is `/str/net/nfs/blackbird/cloudinit`

*Special case*

For local net storage; i.e a gluster volume mounted; use `local` as the host.

Example:  `/str/net/gfs/local/vol1`

## Local Storage

Local storage is to be mounted and kept under `/str/loc` 

The pattern for local storage is as follows:

`/str/loc/{type}/{volume}`

Where `type` is the type of storage. The naming convention for the type of storage is as follows.

- ZFS based Raid array: `ember`

- SSD based Storage: `flame`

- NVME based Storage: `blaze`
  
  This is to allow for clear identification of the type of storage, and it's relative speed. (it also looks cool)

`volume` relates to the name of the volume. For a ZFS filesystem with a volume exported as `glusterstore` the path would be as follows: `/str/loc/zfs/ember/glusterstore`

## Root filesystem

Where possible, the root filesystem externally shall be only 3 partitions. The first being a FAT32 EFI boot partition, mounted at `/boot/efi`. The size shall not exceed 500MB.

This filesystem shall be labelled "EFI"

The second filesystem shall be an EXT4 filesystem mounted at `/boot` not exceeding 10GB. This filesystem shall be named "BOOT"

The third filesystem shall be an LVM based filesystem, taking up the rest of the device. This filesystem shall be named "ROOT"

The LVM partition shall be allocated up to 50GB to the root filesystem, and the rest kept for alternate directories.
