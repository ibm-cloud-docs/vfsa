---

copyright:
  years: 2024
lastupdated: "2024-03-25"

keywords: VLANs, subnets, vFSA, configure

subcollection: vfsa

---

{{site.data.keyword.attribute-definition-list}}

# Configuring your VLAN interfaces and subnets
{: #configure-vlan-subnets}

Before setting a VLAN to "route-through" on the vFSA as described in [Managing VLANs with a gateway appliance](/docs/virtual-router-appliance?topic=virtual-router-appliance-managing-vlans-and-gateway-appliances), you must first configure the VLAN interface(s) and subnet gateway(s) on the FortiGate itself. This process is required for every VLAN you want to route through the vFSA. Every VLAN will have at least one subnet on the VLAN. For each subnet on the VLAN, you will also need to configure the subnet gateway IP on the VLAN interface on the FortiGate.

## Prerequisites
{: #prerequisites}

You will first need to validate that your VLAN is eligible to be protected by the vFSA. For a VLAN to be associated and set to "route-through" for a vFSA, it must be located in the same "Pod" as the vFSA. Each "Pod" number corresponds to the numbered BCR or FCR. For example, pod 1 in the `dal12` datacenter would be within `dal12.bcr01` and `dal12.fcr01`. As such, if you wanted to order a server and protect it with a vFSA provisioned in pod 2 of `Dal13`, when you select the VLAN for your virtual server, you would then select a VLAN prefixed with `dal13.bcr02` for the private VLAN and dal13.fcr02 for the public VLAN.

Second, for each VLAN, you will need to gather the VLAN number. For each subnet on each VLAN, you will also need to gather their subnet gateway IP and subnet mask.

A list of [subnets](https://cloud.ibm.com/networking/subnets){: external} as well as the [VLANs](https://cloud.ibm.com/networking/vlans){: external} on your account can be found in the IBM Cloud console. Clicking on your subnets in the console displays its extended information, including the subnet gateway IP and the subnet mask. In addition, the VLAN's fully qualified name includes information on which "Pod" the VLAN is in.

## VLAN and subnet gateway configuration for an HA vFSA cluster
{: #ha-config}

You can directly configure your public VLANs, private VLANs and subnet gateway IPs on both the FortiGate Web GUI and the [FortiOS CLI](https://docs.fortinet.com/document/fortigate/7.4.3/cli-reference/84566/fortios-cli-reference){: external}. The FortiOS CLI is available through both the Web GUI and SSH. For more information, refer to [this article](https://docs.fortinet.com/document/fortigate/7.4.3/administration-guide/554099/qinq-802-1q-in-802-1q){: external}.

### Configuring your VLAN for HA clusters using the FortiOS CLI
{: #ha-cli-config}

For HA clusters on the vFSA, 4 aggregate interfaces are configured during provisioning:

* `Agg0` is the outside private WAN interface. 
* `Agg1` is the outside public WAN interface. 
* `Agg2` is the inside private interface. 
* `Agg3` is the inside public interface. 

Ensure that you configure any private VLANs with `agg2` as the parent interface. You must also configure any public VLANs with `agg3` defined as the parent interface. 

The following example illustrates the CLI configuration of private VLAN 798, which has a subnet of `10.37.22.0/26`. The subnet gateway for that subnet is `10.37.22.1`, and the subnet mask is `255.255.255.192`. The following example labels the interface using the VLAN number, `b` for backend (private), and `inside`, defining it as an inside VLAN. The `set allowaccess` command allows for control plane protection and stipulates which control/management plane level services that 10.37.22.1/32 can be used for on the FortiGate. In addition, only `ping` is set, as the subnet gateway IP is almost never used for management access to the FortiGate. However, if you want to access the FortiGate using that IP, you can add services (such as, SSH or HTTPS) for remote CLI and Web GUI access respectively. 

```sh
config system interface
edit "v798-b-inside"
set vdom "root"
set ip 10.37.22.1 255.255.255.192
set allowaccess ping
set role lan
set interface "agg2"
set mtu-override enable
set mtu 9000
set vlanid 798
end
```
{: pre}

For any additional subnets on the VLAN, add the subnet gateway and mask as secondary IPs on the interface. For example, if there was another subnet (`10.37.23.0/24`) on VLAN 798, you could use the following commands:

```sh
config system interface
edit v798-b-inside
set secondary-IP enable
config secondaryip
edit 0
set ip 10.37.23.1 255.255.255.0
set allowaccess ping
end
```
{: pre}

The following example illustrates the configuration of a public VLAN (803) on subnet `67.228.192.0/28`. The subnet gateway of that subnet is `67.228.192.1` and the subnet mask is `255.255.255.240`. The following example labels the interface using the VLAN number, `f` for front end (public), and `inside`, defining it as an inside VLAN.

```sh
config system interface
edit "v803-f-inside"
set vdom "root"
set ip 67.228.192.1 255.255.255.240
set allowaccess ping
set role lan
set interface "agg3"
set vlanid 803
end
```
{: pre}

For any additional subnets on the VLAN, add the subnet gateway and mask as secondary IPs on the interface. For example, if there was another subnet (`67.228.225.128/26`) on VLAN 803, you could use the following commands:

```sh
config system interface
edit v803-f-inside
set secondary-IP enable
config secondaryip
edit 0
set ip 67.228.225.129 255.255.255.192
set allowaccess ping
end
```
{: pre}

## VLAN and sUbnet gateway configuration for a stand-alone vFSA
{: #standalone-config}

For the Standalone vFSA, the VLAN interface configurations and the subnet gateway configurations are identical to the HA configurations, with the exception that the parent interface for inside private VLANs is `agg0` and the parent interface for inside public VLANs is `agg1`. In the following examples, the same sample VLANs and subnets from the previous HA example configurations are used.

### Configuring using FortiOS CLI for Standalone vFSA
{: #standalone-cli-config}

You can configure the standalone version in this way:

```sh
config system interface
edit "v798-b-inside"
set vdom "root"
set ip 10.37.22.1 255.255.255.192
set allowaccess ping
set role lan
set interface "agg0"
set mtu-override enable
set mtu 9000
set vlanid 798
end
```
{: pre}

For additional subnet gateways on the same VLAN interface, it is the same as the HA configuration sample:

```sh
config system interface
edit v798-b-inside
set secondary-IP enable
config secondaryip
edit 0
set ip 10.37.23.1 255.255.255.0
set allowaccess ping
end
```
{: pre}

You can configure the standalone version in this way:

```sh
config system interface
edit "v803-f-inside"
set vdom "root"
set ip 67.228.192.1 255.255.255.240
set allowaccess ping
set role lan
set interface "agg1"
set vlanid 803
end
```
{: pre}

The standalone vFSA configuration matches the HA configuration for additional subnet gateways on the same VLAN:

```sh
config system interface
edit v803-f-inside
set secondary-IP enable
config secondaryip
edit 0
set ip 67.228.225.129 255.255.255.192
set allowaccess ping
end
```
{: pre}

## Next steps
{: #configure-vlan-next-steps}

Next, you will need to create [firewall policies](https://docs.fortinet.com/document/fortigate/7.4.3/administration-guide/656084/firewall-policy){: external} to allow the necessary traffic flow.
