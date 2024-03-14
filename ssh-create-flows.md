---

copyright:
  years: 2024
lastupdated: "2024-01-22"

keywords: working, routing, static, default, creating, ospf, bgp

subcollection: vfsa

---

{{site.data.keyword.attribute-definition-list}}

keywords: create, creating, traffic, flows, ssh

subcollection: vfsa

---

{{site.data.keyword.attribute-definition-list}}

# Creating your new traffic flows
{: #creating-your-new-traffic-flows}

Now that you've created the new zone (`CUSTOMER-PUBLIC`), you'll need to configure policies to control the network traffic flow. The first one configured allows all traffic within the `CUSTOMER-PUBLIC` zone. The second allows all traffic from `CUSTOMER-PUBLIC` out to the public internet, while the third flow allows only SSH and PING traffic from the public internet to `CUSTOMER-PUBLIC`, and drops the rest (as the default action is `drop`).
{: shortdesc}

Scroll to the right to view the entire command.
{: important}

```sh
set security policies from-zone CUSTOMER-PUBLIC to-zone CUSTOMER-PUBLIC policy ALLOW_INTERNAL description "Allow all traffic within CUSTOMER_PUBLIC zone"
set security policies from-zone CUSTOMER-PUBLIC to-zone CUSTOMER-PUBLIC policy ALLOW_INTERNAL match source-address any
set security policies from-zone CUSTOMER-PUBLIC to-zone CUSTOMER-PUBLIC policy ALLOW_INTERNAL match destination-address any
set security policies from-zone CUSTOMER-PUBLIC to-zone CUSTOMER-PUBLIC policy ALLOW_INTERNAL match application any
set security policies from-zone CUSTOMER-PUBLIC to-zone CUSTOMER-PUBLIC policy ALLOW_INTERNAL then permit

set security policies from-zone CUSTOMER-PUBLIC to-zone SL-PUBLIC policy ALLOW_OUTBOUND description "Allow all outbound traffic from CUSTOMER-PUBLIC to the internet"
set security policies from-zone CUSTOMER-PUBLIC to-zone SL-PUBLIC policy ALLOW_OUTBOUND match source-address any
set security policies from-zone CUSTOMER-PUBLIC to-zone SL-PUBLIC policy ALLOW_OUTBOUND match destination-address any
set security policies from-zone CUSTOMER-PUBLIC to-zone SL-PUBLIC policy ALLOW_OUTBOUND match application any
set security policies from-zone CUSTOMER-PUBLIC to-zone SL-PUBLIC policy ALLOW_OUTBOUND then permit

set security policies from-zone SL-PUBLIC to-zone CUSTOMER-PUBLIC policy ALLOW_PING_SSH description "Allow ping and SSH from the internet to the public subnet"
set security policies from-zone SL-PUBLIC to-zone CUSTOMER-PUBLIC policy ALLOW_PING_SSH match source-address any
set security policies from-zone SL-PUBLIC to-zone CUSTOMER-PUBLIC policy ALLOW_PING_SSH match destination-address VSI_PUB_NET
set security policies from-zone SL-PUBLIC to-zone CUSTOMER-PUBLIC policy ALLOW_PING_SSH match application junos-ssh
set security policies from-zone SL-PUBLIC to-zone CUSTOMER-PUBLIC policy ALLOW_PING_SSH match application junos-ping
set security policies from-zone SL-PUBLIC to-zone CUSTOMER-PUBLIC policy ALLOW_PING_SSH then permit
```
