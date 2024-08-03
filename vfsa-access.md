---

copyright:
  years: 2024
lastupdated: "2024-08-02"

keywords: vfsa, access

subcollection: vfsa

---

{{site.data.keyword.attribute-definition-list}}

# Accessing your Virtual FortiGate Security Appliance
{: #vfsa-accessing}

The public interfaces of the Ubuntu OS/hypervisor and the vFSA do not allow SSH access. Additionally, the vFSA web GUI is blocked on public by default. You can use several different approaches to access these devices.
{: shortdesc}

## Private Interfaces
{: #vfsa-private-interfaces}

The vFSA Gateway Overview page provides details on the private interfaces of the Ubuntu OS/hypervisor (found in the Hardware pane, under Private IP) and the vFSA (found in the vFSA pane, under Private IP). By default, for the vFSA Private IP, SSH (TCP 22), Ping (ICMP), and the vFSA web GUI (TCP 443) are enabled. To access these interfaces you must have network connectivity to the private network.

The following sections detail ways you can gain access.

## Accessing private interfaces with a VPN
{: #vfsa-vpn-private-interfaces}

IBM Cloud Classic supports shared Virtual Private Networking (VPN) access to the private network. This can be used to access the private interfaces of the host and the vFSA. For more information, refer to [Getting started with IBM Cloud Virtual Private Networking](/docs/iaas-vpn?topic=iaas-vpn-getting-started). Throughput on this shared VPN solution is limited to 1Mbps, and is not recommended for large file transfers. This is the most widely used method for accessing the IBM Cloud Classic private network.

The vFSA itself supports an on board enterprise level [SSL VPN](https://docs.fortinet.com/document/fortigate/7.4.3/administration-guide/559546/ssl-vpn-full-tunnel-for-remote-user){: external} offering that uses FortiClient to connect to the FortiGate VPN endpoint. You can configure this to only access the FortiGate for management access. You can also customize this to allow users to connect to other servers and services both behind and in front of the FortiGate. All configurations require customizing the SSL VPN and the corresponding policies on the FortiGate.

Other custom VPN options include using a classic Baremetal or VSI installed with OpenVPN, WireGuard, or other VPN software to provide an encrypted public access point into the IBM Cloud Classic private network.

## Accessing the private interfaces with a bastion (jump) host
{: #vfsa-bastion-host}

IBM Cloud Classic accounts are generally VRF enabled. If your account is not VRF enabled, either make sure [VLAN spanning](/docs/vlans?topic=vlans-vlan-spanning) is enabled or refer to [Enabling VRF and service endpoints](/docs/account?topic=account-vrf-service-endpoint&interface=ui) to enable VRF. VLAN spanning and VRF are two specific configurations for routing on an account level basis that allow private VLAN intercommunication.

Once either VLAN spanning or VRF is enabled, the cloud resources (such as virtual servers) can connect to each other through their private network connectivity over different private subnets, private VLANs, across pods, and across different datacenters. A VSI with a public facing interface could provide a bridge to the private network, as well as provide access to the private interfaces of the vFSA and bare-metal hosts. You can configure a VSI for this using the VSI as a SOCKS proxy or by configuring the VSI with OpenVPN or WireGuard and using [private secondary subnets](/docs/subnets?topic=subnets-about-subnets-and-ips#static-subnets) as the address pools.

For this particular custom VPN solution, it's important to statically route the secondary private subnets to the VSI's primary private IP.
{: note}

## Using the Ubuntu hosts local console
{: #vfsa-ubuntu-console}

The vFSA runs as a Virtual Machine (VM) on the bare-metal Ubuntu host. You can access the VM using the local console of the Ubuntu host. This requires access to the Ubuntu host, which, by default, has SSH enabled only on the private network.

To access the VM using the local console of the Ubuntu host, find the Qemu serial console port and then telnet to it:

1. Find the VM Name or ID:

   `virsh list --all`.

1. Use the ID to dump the configuration in XML format and search for the console port:

   `virsh dumpxml <id> | grep service`

   For example:

     ```sh
     :~# virsh dumpxml 2 | grep service
         <source mode='bind' host='127.0.0.1' service='8624' tls='no'/>
         <source mode='bind' host='127.0.0.1' service='8624' tls='no'/>
     ```
     {: pre}

1. Use telnet and the port number (from the example) to access the VM:

   `telnet localhost 8624`

You can also run `ss -tlnp | grep qemu` to find the serial console port rather than going through steps 1 and 2.
{: note}

## Enable the public interfaces
{: #enable-public-interfaces}

While it is recommended that you keep your default settings, in order to disable management using public interfaces, it is possible to enable access if needed. The vFSA web GUI can be allowed on public in the `agg1` system interface configuration by adding `https` to the list of allowed services:

```sh
 edit "agg1"
        set vdom "root"
        set ip <public ip> 255.255.255.248
        set allowaccess https fgfm ping
        set type aggregate
        set member "port5" "port6"
        set lldp-transmission enable
        set snmp-index 16
        set lacp-mode static
 next
```
{: pre}

Because of how often port 443 (and web servers in general) are targetted for exploit, you should either keep public ForiOS web GUI access turned off or setup a custom [local in policy](https://docs.fortinet.com/document/fortigate/7.4.3/administration-guide/363127/local-in-policy){: external} to only allow specific IP addresses or ranges to connect. Similar precautions should also be taken when configuring the FortiGate SSL VPN.
{: important}

When executing destructive actions like "Rebuild Cluster" (vFSA Only) and "Force Rebuild" (Ubuntu and vFSA), the defaults will be re-enabled as the systems are reset to defaults.
{: note}
