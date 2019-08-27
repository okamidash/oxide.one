# oxide.one
Oxide One Infrastrucutre redeployment

## The problem
- Subnet is `255.255.0.0`. This results in too many IP addresses
- There is no segregation. Someone on the Client ip range can access the router's admin page
- Domains suck. I have a mixture of `.lan` and `doubledash.org` and `yiff.xyz` for internal only subdomains
- Hostnames aren't properly defined

## Potential Solutions
- Setting up domains for FreeIPA integration

## Proposed Hostnames
### Servers
Blackbird `blackbird.srv.oxide.one`
Inferno `inferno.srv.oxide.one`
Nebula `nebula.srv.oxide.one`

### Domains
oxide.one
 - lan
 - srv
 - vm
 - nix
 - dev
 - adm

#### {hostname}lan.oxide.one [10.0.1.0/24]
This is the DHCP Subnet where clients and temporary virtual machines go.
 
#### {hostname}.srv.oxide.one [10.0.2.0/24]
This is where the baremetal hosts will go.

| hostname  | IP       | CIDR |
|-----------|----------|------|
| nebula    | 10.0.2.1 | /24  |
| blackbird | 10.0.2.2 | /24  |
| inferno   | 10.0.2.3 | /24  |

#### {hostname}.vm.oxide.one [10.0.3.0/24]
This will be for virtual machines. Should always be static

| hostname      | IP       | CIDR |
|---------------|----------|------|
| applejack     | 10.0.3.1 | /24  |
| twilight      | 10.0.3.2 | /24  |
| rainbowdash   | 10.0.3.3 | /24  |
| luna          | 10.0.3.4 | /24  |
| spike         | 10.0.3.5 | /24  |
| celestia      | 10.0.3.6 | /24  |

#### {utility}.nix.oixde.one [10.0.4.0/24]
Utilities and other network critical stuff. NTP, DNS etc.

These will be the secondary IP address of a virtual machine, to allow me to change out virtual machines and just giving them the ip address.

| utility       | IP       | CIDR |
|---------------|----------|------|
| ntp1          | 10.0.4.1 | /24  |
| ntp2          | 10.0.4.2 | /24  |
| dns1          | 10.0.4.3 | /24  |
| dns2          | 10.0.4.4 | /24  |

#### {hostname}.dev.oxide.one [10.0.5.0/24]
Dev subnet for testing shit and messing around.
| hostname      | IP       | CIDR |
|---------------|----------|------|
| pegasus          | 10.0.5.1 | /24  |
| halcyon          | 10.0.5.2 | /24  |
