---

copyright:
  years: 2024
lastupdated: "2024-01-22"

keywords: working, routing, static, default, creating, ospf, bgp

subcollection: vfsa

---

{{site.data.keyword.attribute-definition-list}}

keywords: create, creating, interface, zone, address, subnets, ssh

subcollection: vfsa

---

{{site.data.keyword.attribute-definition-list}}

# Creating the new interface, zone, and address book subnet
{: #creating-the-new-interface-zone-and-address-book-subnet}

First, you'll need to create an interface unit for the VLAN and add the subnet's gateway address. You'll then be able to create a security zone associated with the new unit and an `address-book` entry for the subnet.
{: shortdesc}

The following examples detail a Highly Available (HA) vFSA cluster in both a private and public side configuraton. You must configure all associated private VLANs on `agg2`, and all associated public VLANs on `agg3`. After provisioning, the public WAN connection will be configured on `agg1`, and the private WAN connection on`agg0`. For each subnet on an associated VLAN, the subnet gateway needs to be configured on the VLAN interface(s). The second IP in the customer subnet, immediately following the network ID, is the subnet gateway. In the following example, `10.186.228.145/28` is the subnet gateway of `10.186.228.144/28` on the private VLAN, `1898`.

For more information on associated VLANS, refer to [Managing VLANs with a gateway appliance](/docs/vfsa?topic=gateway-appliance-managing-vlans-and-gateway-appliances).
{: note}

Private side configuration example:

```sh
set interfaces agg2 vlan-tagging
set interfaces agg2 redundant-ether-options redundancy-group 1
set interfaces agg2 unit 1898 vlan-id 1898
set interfaces agg2 unit 1898 family inet address 10.186.228.145/28
set interfaces agg2 unit 1898 family inet address 10.208.110.121/29
set interfaces agg2 unit 1898 family inet address 10.208.237.97/29
set security zones security-zone SL-PRIVATE interfaces agg2.1898 host-inbound-traffic system-services all
set security address-book global address VSI_PRIV_NET 10.208.237.96/29
set security address-book global address VSI_PRIV_NET1 10.186.228.144/28
set security address-book global address VSI_PRIV_NET2 10.208.110.120/29
```
{: codeblock}

Public side configuration example:

```sh
set interfaces agg3 vlan-tagging
set interfaces agg3 redundant-ether-options redundancy-group 1
set interfaces agg3 unit 1523 vlan-id 1523
set interfaces agg3 unit 1523 family inet address 169.47.211.153/29
set security zones security-zone CUSTOMER-PUBLIC interfaces agg3.1523 host-inbound-traffic system-services all
set security address-book global address VSI_PUB_NET 169.47.211.152/29
```
{: codeblock}

## Filtering on loopback considerations
{: #loopback-considerations}

The `PROTECT-IN` filter on loopback blocks (by default) all local traffic traveling to and from the vFSA that hasn't been explicitly allowed. As a result, in order to ping the subnet gateways defined previously, or to ping servers on those subnets from the vFSA, the subnet gateways must be added to `PROTECT-IN`. This is configured by default for the `agg1` and `agg0` IP addresses and is why they are accessible after provisioning.


```sh
set firewall filter PROTECT-IN term PING from destination-address 10.208.237.97/32
set firewall filter PROTECT-IN term PING from destination-address 10.208.110.121/32
set firewall filter PROTECT-IN term PING from destination-address 10.186.228.145/32
set firewall filter PROTECT-IN term PING from destination-address 169.47.211.153/32
set firewall filter PROTECT-IN term PING from protocol icmp
set firewall filter PROTECT-IN term PING then accept
```
{: codeblock}
