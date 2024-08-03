---

copyright:
  years: 2024
lastupdated: "2024-08-02"

keywords: firewalls, working, policy, policies, rules, zones, standalone, ha

subcollection: vfsa

---

{{site.data.keyword.attribute-definition-list}}

# Working with firewalls
{: #working-with-firewalls}

The {{site.data.keyword.vfsa_full}} uses firewall policies to control what network traffic can flow through the firewall. The parameters of the network traffic are matched against the configured policies. If a match is not found the traffic is denied.
{: shortdesc}

Fortinet provides an [overview (https://docs.fortinet.com/document/fortigate/7.4.3/administration-guide/656084/firewall-policy) of firewall policies, as well as information on how to configure them through the GUI and CLI.
{: tip}

## Default firewall policies
{: #default-firewall-policies}

The {{site.data.keyword.vfsa_full}} comes with a default set of firewall policies. These policies are based on the packet's incoming/ingress (source) interface and outgoing/egress (destination) interface. The policies also match on the source and destination addresses assigned to the vFSA and the service network, as well as the type of traffic (ICMP, HTTPS, and so on). Table 1 illustrates the default firewall policies for both High Availability (HA) and standalone (SA) vFSAs. When the traffic parameters match these policies, traffic is accepted. Otherwise, it is denied.

To see the network address objects that correlate to the source and destination addresses referenced in the following table, run the command `show firewall address`.

To see the network address group that correlates to the source and destination addresses referenced in the table, run the command `show firewall addrgrp`.

| Source Interface|Destination interface|Source address|Destination address|Services|
| :---|:----:|:----:|:---:|---:|
|`agg0` (private untagged)|`agg0`|`SL-PRIVATE` (service network)|`SL_PRIV_MGMT` (Private IP assigned to the vFSA cluster)|All|
|`agg0` (private untagged)|`agg0`|`SL-PRIVATE` (service network)|`SERVICE` (Address group for service network)|All|
|`agg1` (public untagged)|`agg1`|All| `SL_PUB_MGMT` (Public IP assigned to the vFSA cluster)|`PING`, `HTTPS`|
{: caption="Table 1. Default firewall policies for vFSA" caption-side="bottom"}

## VLAN firewall policies
{: #vlan-firewall-policies}

The topic [Configuring your VLAN interfaces and subnets](/docs/vfsa?topic=vfsa-configure-vlan-subnets) discusses how to configure interfaces and subnets for the VLANs you are routing through the vFSA. Once you complete this configuration, you must also configure firewall policies to control the traffic flows on these interfaces. It is recommended that you wait to route the VLANs through the vFSA until you have configured the firewall policies. Otherwise, traffic will be denied by default if the VLAN routes through with no policies in place. The following example illustrates the requirements:

This example assumes two private VLAN's, 781 and 794, with the following configured sub-interfaces:

```sh
config system interface
edit "VLAN_781"
    set vdom "root"
    set ip 10.9.16.65 255.255.255.192
    set allowaccess ping https ssh http
    set device-identification enable
    set role lan
    set mtu-override enable
    set interface "agg2"
    set vlanid 781
next
edit "VLAN_794"
    set vdom "root"
    set ip 10.9.23.65 255.255.255.192
    set allowaccess ping https ssh http
    set device-identification enable
    set role lan
    set mtu-override enable
    set interface "agg2"
    set vlanid 794
next
end
```
{: pre}

Create firewall policies to allow both ingress and egress traffic between the sub-intefaces and the private service (transit) network on interface `agg0`. In addition, this allows all services on these interfaces:

```sh
config firewall policy
    edit 4
        set srcintf "VLAN_781"
        set dstintf "VLAN_794"
        set srcaddr "all"
        set dstaddr "all"
        set action accept
        set schedule "always"
        set service "ALL"
    next
    edit 5
        set srcintf "VLAN_794"
        set dstintf "VLAN_781"
        set srcaddr "all"
        set dstaddr "all"
        set action accept
        set schedule "always"
        set service "ALL"
    next
    edit 6
        set srcintf "agg0"
        set dstintf "VLAN_781"
        set srcaddr "all"
        set dstaddr "all"
        set action accept
        set schedule "always"
        set service "ALL"
    next
    edit 7
        set srcintf "VLAN_781"
        set dstintf "agg0"
        set srcaddr "all"
        set dstaddr "all"
        set action accept
        set schedule "always"
        set service "ALL"
    next
    edit 8
        set srcintf "agg0"
        set dstintf "VLAN_794"
        set srcaddr "all"
        set dstaddr "all"
        set action accept
        set schedule "always"
        set service "ALL"
    next
    edit 9
        set srcintf "VLAN_794"
        set dstintf "agg0"
        set srcaddr "all"
        set dstaddr "all"
        set action accept
        set schedule "always"
        set service "ALL"
    next
end
```
{: pre}

Next, create a firewall policy that blocks `ping` (icmp) from sub-interface `VLAN_781` to sub-interface `VLAN_794`:

```sh
config firewall policy
edit 10
    set name "Block-PING-781-TO-794"
    set srcintf "VLAN_781"
    set dstintf "VLAN_794"
    set srcaddr "VLAN_781 address"
    set dstaddr "VLAN_794 address"
    set schedule "always"
    set service "PING"
    set logtraffic all
next
end
```
{: pre}

Ensure policy 10 is higher priority than policy 4, otherwise it will not take effect:

```sh
config firewall policy
move 10 before 4
end
```
{: pre}

Create a policy that blocks only HTTP requests on port `9004` from sub-interface `VLAN_794` to sub-interface `VLAN_781`. First, create a custom firewall service that can be referenced in the policy:

```sh
config firewall service custom
    edit "HTTP-9004"
        set protocol TCP
        set tcp-portrange 9004-9004
    next
end
```
{: pre}

Next, create the policy:

```sh
config firewall policy
edit 11
    set name "Block-HTTP"
    set srcintf "VLAN_794"
    set dstintf "VLAN_781"
    set srcaddr "VLAN_794 address"
    set dstaddr "VLAN_781 address"
    set schedule "always"
    set service "HTTP-9004"
    set logtraffic all
next
end
```
{: pre}

Traffic logs can be viewed from the vFSA Web Management Console by selecting **Log & Report > Forward Traffic**.
