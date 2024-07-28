---

copyright:
  years: 2024
lastupdated: "2024-01-22"

keywords: working, routing, static, default, creating, ospf, bgp

subcollection: vfsa

---

{{site.data.keyword.attribute-definition-list}}

# Working with routing
{: #working-with-routing}

The {{site.data.keyword.vfsa_full}} is based on `JunOS`, giving you access to the full Juniper routing stack.
{: shortdesc}

## Static routing
{: #static-routing}

To configure static routes, run the following commands:

### Setting a default route
{: #setting-a-default-route}

```sh
set routing-options static route 0/0 next-hop <Gateway IP>
```

### Creating a static route
{: #creating-a-static-route}

```sh
set routing-options static route <PREFIX/MASK> next-hop <Gateway IP>
```

### Basic OSPF routing
{: #basic-ospf-routing}

To setup basic OSPF routing, only using area 0, run the following commands using md5 authentication:

```sh
set protocols ospf area 0 interface ge-0/0/1.0 authentication md5 0 key <KEY>
```

### Basic BGP routing
{: #basic-bgp-routing}

To setup basic BGP routing, first define the local AS:

```sh
set routing-options autonomous-system 65001
```

Then configure the BGP neighbor and its session attributes:

```sh
set protocols bgp group CUSTOMER local-address 1.1.1.1
set protocols bgp group CUSTOMER family inet unicast
set protocols bgp group CUSTOMER family inet6 unicast
set protocols bgp group CUSTOMER peer-as 65002
set protocols bgp group CUSTOMER neighbor 2.2.2.2
```

In this example, BGP is configured for the following:

* To use source IP address of `1.1.1.1` to establish the session
* To negotiate both ipv4 and ipv6 unicast families
* To peer with a neighbor that belongs to `AS 65002`
* Peer neighbor IP `2.2.2.2`

Additional configurations can be found on the [Juniper website](https://www.juniper.net/documentation/product/us/en/junos-os/){: external}.
