---

copyright:
  years: 2024
lastupdated: "2024-01-22"

keywords: working, failover, codes, failure, cli

subcollection: vfsa

---

{{site.data.keyword.attribute-definition-list}}

# Working with failover
{: #working-with-failover}
{: help}
{: support}

You can initiate failover from your primary {{site.data.keyword.vfsa_full}} to a backup device so that all control and data plane traffic is routed through the secondary gateway device after failover.
{: shortdesc}

This section is only applicable if your Juniper vFSA gateway devices are provisioned in High-Availability mode.
{: note}

To do so, follow these steps:

1. Log in to your primary vFSA gateway device.

1. Enter CLI mode by running the command `cli` at the console prompt. When you enter CLI mode, the console displays the node role, either `primary` or `secondary`.

	Ensure that you are in the `primary` node. If you are not, exit and login to the other vFSA gateway device of the pair.

1. On the primary vFSA gateway device, run the command `show chassis cluster status`.

   The output is similar to the following:

	```text
	Monitor Failure codes:
		CS  Cold Sync monitoring        FL  Fabric Connection monitoring
		GR  GRES monitoring             HW  Hardware monitoring
		IF  Interface monitoring        IP  IP monitoring
		LB  Loopback monitoring         MB  Mbuf monitoring
		NH  Nexthop monitoring          NP  NPC monitoring
		SP  SPU monitoring              SM  Schedule monitoring
		CF  Config Sync monitoring

	Cluster ID: 2
	Node   Priority Status         Preempt Manual   	Monitor-failures

	Redundancy group: 0 , Failover count: 1
	node0  100      primary        no      no       None
	node1  1        secondary      no      no       None
	Redundancy group: 1 , Failover count: 1
	node0  100      primary        yes     no       None
	node1  1        secondary      yes     no       None

	{primary:node0}
	```

	Ensure that, for both redundancy groups, the same node is set as `primary`. It is possible for different nodes to be set as the `primary` role in different redundancy groups.

	The vFSA, by default, sets `Preempt` to `yes` for Redundancy group 1, and `no` for Redundancy group 0. Refer to [this link](https://www.juniper.net/documentation/us/en/software/junos/chassis-cluster-security-devices/topics/topic-map/security-chassis-cluster-redundancy-group-failover.html){: external} to learn more about pre-emption and failover behavior.
	{: note}

1. Initiate failover by running the following command in the console prompt:

	```sh
	request chassis cluster failover redundancy-group <redundancy group number> node <node number>
	```

	Select the appropriate redundancy group number and node number from the output of the command in step two. To failover both redundancy groups, run  the previous command twice, one for each group.

1. After failover is complete, verify the console output. It should now be listed as `secondary`.

1. Log in to the other vFSA gateway of your pair. Enter CLI mode by again running the command `cli` and then verify that the console output shows as `primary`.

When you enter CLI mode in your Juniper vFSA gateway device, the output shows as `primary` from the control plane perspective. Always check the `show chassis cluster status` output to determine which gateway device is primary from data plane perspective. Refer to [vFSA Default Configuration](/docs/vfsa?topic=vfsa-understanding-the-vfsa-default-configuration) to learn more about redundancy groups, as well as the control and data planes.
{: tip}
