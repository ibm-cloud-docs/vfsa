---

copyright:
  years: 2024
lastupdated: "2024-03-14"

keywords: about, overview, details, firewall, vpn, nat, routing, vlan

subcollection: vfsa

---

{{site.data.keyword.attribute-definition-list}}

# About IBM Cloud Virtual FortiGate Security Appliance
{: #about-ibm-cloud-vfsa}

The {{site.data.keyword.vfsa_full}} (vFSA) is deployed by using the {{site.data.keyword.cloud}} Gateway Appliance and Bare Metal Servers offerings. The Bare Metal servers are provisioned with the standard Ubuntu image and then customized with an SR-IOV-enabled KVM virtual machine (VM) running the FortiGate VM image. 
{: shortdesc}

This offering provides the availability, dedicated power, and disaster recovery from in-house Bare Metal servers, while also providing the capabilities of FortiOS and FortiGates. The VM and its hardware are sized to be able to handle and protect many inside public and private VLANs and the servers and services running on them. The appliance comes with dual aggregated private and public network interfaces. You can manage and customize vFSA features to fit your requirements. This includes maintaining the correct versions of Ubuntu OS and hypervisor, while ensuring that they are locked down and monitored for hardware failure. This also includes the configuration of VLANs, subnets, required security policies, and security profiles to fit your security needs.

The {{site.data.keyword.vfsa_full}} is offered as a single stand-alone appliance or as two appliances in a High Availability (HA) cluster. It is also offered with either two 1-Gigabit Ethernet (1 GbE) aggregated public NICs and two 1 GbE private NICs, or as two 10 GbE public and two 10 GbE private NICs. When you order, this is called public and private interfaces with "automatic" port redundancy. The port redundancy is set up with Link Aggregation Control Protocol (LACP).

## Firewall
{: #about-firewall}

The vFSA protects your environment from external and internal threats by filtering both private and public traffic. You can manage the vFSA itself by defining policies that accept or deny inbound or outbound IP network traffic. This protects your applications from internal and external threats. You can also add security profiles to your security policies to use advanced firewall features like antivirus, intrustion prevention, WAF, web filtering, and deep packet inspection. 

## Integration with FortiGuard AI-powered security services
{: #about-fortiguard}

The vFSA includes [FortiGuard](https://www.fortinet.com/solutions/enterprise-midsize-business/security-as-a-service/fortiguard-subscriptions){: external} AI-powered security services. vFSA services that use FortiGuard include IPS, antivirus, advanced malware protection, web filtering, application control, antispam, and FortiSandbox Cloud.

To use these services, ensure that your license includes these services.
{: tip}

## Virtual private network (VPN) gateway
{: #virtual-private-network-vpn-gateway}

You can connect your onsite data center or office to IBM Cloud by using IPsec site-to-site VPN or BGP over GRE tunnels. You can use an IPsec site-to-site VPN tunnel for secure communication from your enterprise data center or office to your IBM Cloud network if an encrypted and secure connection is required over the public internet. You can also use BGP over GRE if an encrypted connection is not needed over private networks when using offerings, such as Direct Link (that do not traverse the public internet).

You can use FortiClient with your FortiGate to establish a remote access VPN connection into the IBM Cloud Classic infrastructure environment. Except for SSL VPN, other offerings can use different versions of FortiClient for features, such as endpoint protection.

## Network address translation (NAT)
{: #network-address-translation-nat-}

You can provision application and database servers behind the {{site.data.keyword.vfsa_full}} without public network interfaces while still allowing your servers access to the internet through Source NAT (SNAT or PAT). Fortinet accomplishes this by using NAT-enabled firewall policies. For enhanced security, you can protect your private-network-only servers behind the gateway device, using `_destination NAT_`. This maps an outside public IP to the inside private IP.

## Enterprise-grade routing
{: #enterprise-grade-routing}

{{site.data.keyword.vfsa_full}} gives you flexibility to build connectivity between multi-tiered applications running on different isolated networks. You can set up dynamic routing by using BGP over GRE, which allows you to advertise your cloud IP space and receive advertisements from your own remote side firewall or router endpoints. This is frequently used and integrated with [Direct Link solutions](/docs/dl?topic=dl-get-started-with-ibm-cloud-dl) for connections into power and on-prem environments.

## Concepts about VLANs and the gateway appliance's role
{: #concepts-about-vlans}

A VLAN (virtual local area network) is a mechanism that segregates a physical network into many virtual segments. For convenience, traffic from multiple selected VLANs can be delivered through a single or aggregated switchport, using a process called "trunking."

The IBM Cloud vFSA is managed using two separate components: 

* The vFSA servers (Bare Metal and FortiGate VM)
* The [Gateway Appliance section of the IBM Cloud console](/netsec/gateway-appliances)

Instead of using the IBM Cloud console, you can also use the [SLAPI](https://sldn.softlayer.com/reference/services/SoftLayer_Network_Gateway/){: external} for the same functionality. 
{: tip}

The gateway appliance section of the IBM Cloud console provides an interface for selecting the VLANs that you want to associate with your vFSA. Associating and then setting a VLAN to "route through" does two things: 

* Trunks or allows the VLANs on all relevant data center switches
* Routes all of the VLAN's subnets statically to your vFSA's IP address

This gives you control over those VLANs and the servers and IP addresses provisioned for them. Servers in an associated or routed VLAN can be reached from other VLANs only by going through your vFSA. Servers on different subnets (while on the same VLAN) must also communicate through the vFSA. In those cases, to circumvent the vFSA, you must bypass or disassociate the VLAN. However, if your servers are on the same subnet, traffic between them bypass the vFSA and use only the layer 2 switch path.

By default, a new gateway appliance is provisioned on two nonremovable "transit" VLANs, one each for your public and private networks. These "transit" VLANs can have only gateway appliances that are provisioned on them. The public and private networks on the transit VLANs are used by the upstream routers and vFSA for the point-to-point connection between the upstream routers and the vFSA.

## Integration with the Fortinet Fabric
{: #fortinet-fabric}

Unlike with the 10 G FSA, the vFSA includes superuser access. As a result, you can integrate your FortiGate into your Fortinet Fabric by connecting your FortiGate to many other virtual or hardware appliances and services, such as: 

* FortiManager, for centralized management and administration of multiple FortiGates through provisioning and automation tools
* FortiAnalyzer, for centralized reporting, log management, and analytics for multiple FortiGates
* FortiSandbox, for isolating and analyzing suspicious files using AI and a dedicated appliance or virtual appliance

   FortiGate Cloud Sandboxing is included with every available license.
   {: note}

* FortiAuthenticator, for centralized identity and access management
* FortiClient for SSL-VPN (initially included), IPSec VPN (initially included), secure access using zero trust network access (ZTNA), endpoint protection using AI-based next-generation antivirus, and quarantining and endpoint management
* FortiClient EMS, for central management of devices running FortiClient

Unless noted, these Fortinet offerings are not initially included with the vFSA.
{: note}
