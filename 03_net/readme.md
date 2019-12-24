# Section 3 | Networks

Section 3 covers the network layout.

## 03.01 - Networks

There are 9 networks that exist within the oxide.one subsystem. 

They are outlined below.

| Name       | Subnet       | DNS            | Vlan | Purpose          |
| ---------- | ------------ | -------------- | ---- | ---------------- |
| outer      | 10.0.0.0/24  | dhcp.oxide.one | 0    | Outer Network    |
| inner      | 10.0.20.0/24 | N/A            | 0    | Inner network    |
| server     | 10.0.2.0/24  | srv.oxide.one  | 2    | Physical Hosts   |
| virtual    | 10.0.4.0/24  | vm.oxide.one   | 4    | Virtual Machines |
| utility    | 10.0.5.0/24  | nix.oxide.one  | 5    | Utilities        |
| zoned      | 10.0.6.0/24  | zone.oxide.one | 6    | Untrusted Subnet |
| kube       | 10.0.7.0/24  | kube.oxide.one | 7    | Kubernetes       |
| wireguard  | 10.0.8.0/24  | wg.oxide.one   | 8    | VPN Subnet       |
| management | 10.0.10.0/24 | mgmt.oxide.one | 10   | Management       |

### Network | Outer

The *outer* network exists on the subnet 10.0.0.0/24 and on it contains the wider network. This is the subnet which all internet bound applications traverse through.

### Network | Inner

The *inner* network is the default network for machines that do not have a vlan configuration setup. This network is only used when provisioning physical hosts, as they often will not have a VLAN configured, and will need access to *a* network to configure them.

### Network | Server

The *server* network is the network for physical hosts. All physical hosts are to have an assigned, static IP address. Any machines configured on this network should be reachable by their hostname + dns name. While DHCP does run on this network, it should not be relied upon.

### Network | Virtual

The *virtual* network exists to serve nondescript virtual machines. Unless there is a specific reason not to; virtual machines should live on this subnet and have their IP provided by the DHCP server.

### Network | Utility

The *utility* network is for virtual machines that serve to provide utilities to other virtual machines. Virtual machines on this network should have a static IP. Some examples of Virtual Machines that would exist on this subnet are: FreeIPA (DNS), Mail Server, HaProxy.

### Network | Zoned

The *zoned* network is for untrusted machines. These machines do not have any access to any other networks, only to the internet. This network is used for things like Metasploitable.

### Network | Kube

The *kube* network is for the virtual machines that underpin kubernetes clusters. They are kept outside the virtual machine network because each kubernetes cluster might also require load balancer IPs, and there might be IP address collisions if kept together.

### Network | Wireguard

The *wireguard* network is for physical and virtual machines that require internet access through a vpn. Any machine on this network will have internet access to other networks, but any internet bound activity will go through a wireguard tunnel to a VPN provider.

### Network | Management

The *management* network is for physical machines that expose management interfaces such as IPMI, Routers or Switches. This network has access to all other networks.



## 03.02 - Network Tools

### About

The DNS servers for all networks should resolve to the central DNS server located on the *utility* network.  DNS Resolution is provided by a FreeIPA server, underpinned by BIND9. The DHCP server for each network should handle DNS updates to the central DNS server; so that remembering the IP address of each machine is redundant.

### DHCP

For the *outer* network, DHCP is provided by [dnsmasq](http://www.thekelleys.org.uk/dnsmasq/doc.html) running on AsusWRT. 

For the *inner* network, DHCP is provided by [RouterOS](https://mikrotik.com/software)

For *all other* networks, DHCP is handled by the [ISC DHCP](https://www.isc.org/dhcp/) daemon (DHCPD). 

For networks handled by DHCPD, the DHCP start range will be .50-.240 to allow for flexibility for static IP addresses to be set in edge cases.

### Nsupdate

To be able to update the DNS record for each machine on the networks, the DHCP server will use nsupdate to update the record. This cannot easily be done for both the *outer* and *inner* networks, but can be done for all other networks.

An example code block is provided below for DNS updates to be sent to the DNS server. 

On the *virtual* network; the relevant code in `/etc/dhcp/dhcpd.conf` would look like the following.

```
use-host-decl-names on;
ddns-update-style interim;
key rndc-key { algorithm REDACTED; secret REDACTED; };

zone vm.oxide.one. { primary 10.0.5.2; key rndc-key; }
zone 4.0.10.in-addr.arpa. { primary 10.0.5.2; key rndc-key; }

shared-network vm {
    subnet 10.0.4.0 netmask 255.255.255.0 {
        option domain-name-servers 10.0.5.2;
        option domain-search "vm.oxide.one";
        option routers 10.0.4.1;
        option domain-name "vm.oxide.one";
        default-lease-time 86400;
        max-lease-time 86400;
        range 10.0.4.50 10.0.4.240;
    }
}
```

For this code to update successfully using the nsupdate key, FreeIPA needs to have the following line configured in it's `/etc/named.conf` 

`include "/etc/rndc.key";`

Additionally, login to the FreeIPA portal, and add the following lines into the 'BIND update policy' section of each DNS zone you want to allow DNS updates from.

`grant "rndc-key" zonesub ANY;`



## 03.03 - Network Setup

Networks are managed across the oxide.one realm with Network Manager.

The 'stack' shall behave as follows.

physical interface -> vlan interface -> bridge interface.

The physical interface should retain it's original name.

The VLAN interface shall be named by the interfaces name, with '_{VLAN}'' appended to it.

The Bridge interface shall be named after the network.

Using Nmcli, you can create these interfaces using the following lines.

```shell
nmcli con add type bridge con-name zoned ifname zoned
nmcli con add type vlan con-name vlan-zoned ifname vlan-zoned dev enp131s0 id 10 master zoned slave-type bridge
```


