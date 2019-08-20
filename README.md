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
