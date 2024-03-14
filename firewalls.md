---

copyright:
  years: 2024
lastupdated: "2024-01-22"

keywords: firewalls, working, policy, policies, rules, zones, standalone, ha

subcollection: vfsa

---

{{site.data.keyword.attribute-definition-list}}

# Working with firewalls
{: #working-with-firewalls}

The {{site.data.keyword.vfsa_full}} uses the concept of security zones, where each vFSA interface is mapped to a "zone" for handling stateful firewalls. Stateless firewalls are controlled by firewall filters.
{: shortdesc}

Policies are used to allow and block traffic between these defined zones, and the rules defined here are stateful.

In the IBM Cloud, a vFSA is designed to have four different security zones:

| Zone                     | Standalone Interface | HA Interface |
| :---                     |        :----:        |         ---: |
| SL-Private (untagged)    | ge-0/0/0.0 or agg0.0  | agg0.0      |
| SL-Public (untagged)     | ge-0/0/1.0 or agg1.0  | agg1.0      |
| Customer-Private (tagged)| ge-0/0/0.1 or agg0.1  | agg2.1      |
| Customer-Public (tagged) | ge-0/0/1.1 or agg1.1  | agg3.1      |
{: caption="Table 1. Security zones" caption-side="bottom"}

## Zone policies
{: #zone-policies}

To configure a stateful firewall, follow these steps:

1. Create security zones and assign the respective interfaces:

   Standalone scenario:

	 ```sh
	 set security zones security-zone CUSTOMER-PRIVATE interfaces ge-0/0/0.1
	 set security zones security-zone CUSTOMER-PUBLIC interfaces ge-0/0/1.1
   ```

   High Availability scenario:

   ```sh
   set security zones security-zone CUSTOMER-PRIVATE interfaces agg2.1
   set security zones security-zone CUSTOMER-PUBLIC interfaces agg2.1
   ```

1. Define the policy and rules between two different zones.

	The following example illustrates pinging traffic from the zone `Customer-Private` to `Customer-Public`:

	```sh
	set security policies from-zone CUSTOMER-PRIVATE to-zone CUSTOMER-PUBLIC policy ALLOW_ICMP match source-address any destination-address any application junos-icmp

	set security policies from-zone CUSTOMER-PRIVATE to-zone CUSTOMER-PUBLIC policy ALLOW_ICMP then permit
	```

These are some of the attributes that can be defined in your policies:

* Source addresses
* Destination addresses
* Applications
* Action (permit/deny/reject/count/log)

Since this is a stateful operation, there is no need to allow return packets (in this case, the echo replies).

Use the following commands to allow traffic that is directed to the vFSA:

Standalone case:

```sh
set security zones security-zone CUSTOMER-PRIVATE interfaces ge-0/0/0.0 host-inbound-traffic system-services all
```

HA case:

```sh
set security zones security-zone CUSTOMER-PRIVATE interfaces agg2.0 host-inbound-traffic system-services all
```

To allow protocols, such as OSPF or BGP, use the following command:

Standalone case:

```sh
set security zones security-zone trust interfaces ge-0/0/0.0 host-inbound-traffic protocols all
```

HA case:

```sh
set security zones security-zone trust interfaces agg2.0 host-inbound-traffic protocols all
```

## Firewall filters
{: #firewall-filters}

By default the {{site.data.keyword.vfsa_full}} allows ping, SSH, and HTTPS to itself and drops all other traffic by applying the `PROTECT-IN` filter to the `lo` interface.

To configure a new stateless firewall, follow these steps:

1. Create the firewall filter and term (the following filter allows only ICMP and drops all other traffic):

   ```sh
   set firewall filter ALLOW-PING term ICMP from protocol icmp
   set firewall filter ALLOW-PING term ICMP then accept
   ```

1. Apply the filter rule to the interface (the following command applies the filter to all private network traffic):

	```sh
	set interfaces ge-0/0/0 unit 0 family inet filter input ALLOW-PING
	```
