# Filesystem Layout

For all machines, physical and virtual; storage specific directories are kept in the `/stg` directory. 

The following directories are to be made when a system is provisioned.

`/str/loc`

`/str/net`



**Network Storage**

Network based storage is kept under `/str/net`

The pattern followed for network storage is as follows:

/str/net/{type}/{host}/{volume}

Where `type` is the method of storage, abbrevieated to 3 letters. It is good practice to keep the first character unique, so that tab completion is easier.

- Network FIle System (NFS): `/str/net/nfs`

- Gluster FS: `/str/net/gfs`

- Ceph Storage: `/str/net/cep`



`host` relates to where the storage is kept. For a NFS based storage exported on the host 'blackbird' the directory would be `/str/net/nfs/blackbird/`

`volume` relates to the name of the share/volume exported.  Continuing the previous example, for a share called `cloudinit` the full storage path is `/str/net/nfs/blackbird/cloudinit`



*Special case*

For local net storage; i.e a gluster volume mounted; use `local` as the host.

Example:  `/str/net/gfs/local/vol1`



**Local Storage**

Local storage is to be mounted and kept under `/str/loc` 

The pattern for local storage is as follows:

`/str/loc/{type}/{volume}/path`

Where `type` is the type of storage. The naming convention for the type of storage is as follows.

- ZFS based Raid array: `ember`

- SSD based Storage: `flame`

- NVME based Storage: `blaze`
  
  This is to allow for clear identification of the type of storage, and it's relative speed. (it also looks cool)

`volume` is the name of the volume. 

The purpose of this naming scheme is to allow for easy identification and 








