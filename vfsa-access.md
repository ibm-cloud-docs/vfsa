---

copyright:
  years: 2024
lastupdated: "2024-03-05"

keywords: vfsa, access

subcollection: vfsa

---

{{site.data.keyword.attribute-definition-list}}

# Accessing your FortiGate Virtual Security Appliance
{: #vfsa-accessing}

The public interfaces of the Ubuntu hypervisor and the vFSA do not allow SSH access. Additionally, the vFSA web management console is disabled by default. You can use several different approaches to access these devices.
{: shortdesc}

## Private Interfaces
{: #vfsa-private-interfaces}

The vFSA Gateway Overview page provides details on the private interfaces of the Ubuntu hypervisor (found in the Hardware pane, under Private IP) and the vFSA (found in the vFSA pane, under Private IP). By default, SSH, Ping (ICMP), and the vFSA web management interface (8443) are enabled on these interfaces. To access these interfaces you must have network connectivity to the private network. 

The following sections detail ways you can gain access.

## Accessing private interfaces with a VPN
{: #vfsa-vpn-private-interfaces}

IBM Cloud Classic supports Virtual Private Networking (VPN) access to the private network. This can be used to access the private interfaces of the host and the vFSA. For more information, refer to [Getting started with IBM Cloud Virtual Private Networking](/docs/iaas-vpn?topic=iaas-vpn-getting-started).

## Accessing the private interfaces with a bastion (jump) host
{: #vfsa-bastion-host}

IBM Cloud Classic accounts are generally VRF enabled. If your account is not enabled, refer to [Enabling VRF and service endpoints](/docs/account?topic=account-vrf-service-endpoint&interface=ui). 

Once enabled, the cloud resources use a [multiple isolation model](/docs/dl?topic=dl-overview-of-virtual-routing-and-forwarding-vrf-on-ibm-cloud), allowing for private network connectivity between data centers and pods. A VSI with a public facing interface could provide a bridge to the private network, as well as provide access to the private interfaces of the vFSA and bare-metal hosts.

## Using the Ubuntu hosts local console
{: #vfsa-ubuntu-console}

The vFSA runs as a Virtual Machine (VM) on the bare-metal Ubuntu host. You can access the VM using the local console of the Ubuntu host. This requires access to the Ubuntu host, which, by default, has SSH enabled only on the private network. 

To access the VM using the local console of the Ubuntu host:

1) Find the VM Name or ID: 

   `virsh list --all`.
   
2) Use the ID to dump the configuration in XML format and search for the console port: 

   `virsh dumpxml <id> | grep service`

For example:
  ```
  :~# virsh dumpxml 2 | grep service
      <source mode='bind' host='127.0.0.1' service='8624' tls='no'/>
      <source mode='bind' host='127.0.0.1' service='8624' tls='no'/>  
  ```
  {: pre}
  
3) Use telnet and the port number (from the example) to access the VM:

   `telnet localhost 8624`
  
## Enable the public interfaces

While it is recommended to keep your default settings so as to disable the public interfaces, it is possible to re-enable them if needed. The vFSA web management interface can be enabled in the reth1 system interface configuration by adding `https` functionality:

```
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

When executing destructive actions like "Rebuild Cluster" (vFSA Only) and "Force Rebuild" (Ubuntu and vFSA), the defaults will be re-enabled as the systems are reset to defaults.
{: note}


