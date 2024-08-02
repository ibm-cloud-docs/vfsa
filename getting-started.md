---

copyright:
  years: 2024
lastupdated: "2024-03-05"

keywords: ordering, gateway, appliance, vfsa, license

subcollection: vfsa

---

{{site.data.keyword.attribute-definition-list}}

# Getting started with IBM Cloud Virtual FortiGate Security Appliance
{: #getting-started}

{{site.data.keyword.vfsa_full}} allows you to route private and public network traffic selectively, through a full-featured, enterprise-level firewall that is powered by [FortiOS](https://www.fortinet.com/products/fortigate/fortios) software features and [FortiGuard AI-powered Security Services](https://www.fortinet.com/solutions/enterprise-midsize-business/security-as-a-service/fortiguard-subscriptions). 
{: shortdesc}

These features include:

* [Firewall policies](https://docs.fortinet.com/document/FortiGate/7.4.3/administration-guide/656084/firewall-policy)
* [Dynamic routing protocols](https://docs.fortinet.com/document/FortiGate/7.4.3/administration-guide/479509/dynamic-routing)
* [Intrusion Prevention Service (IPS)](https://www.fortinet.com/support/support-services/fortiguard-security-subscriptions/intrusion-prevention)
* [Deep packet and SSL Inspection (DPI)](https://www.fortinet.com/resources/cyberglossary/dpi-deep-packet-inspection)
* [Anti-malware and antivirus support](https://www.fortinet.com/support/support-services/fortiguard-security-subscriptions/antivirus)
* [DNS filtering](https://www.fortinet.com/support/support-services/fortiguard-security-subscriptions/dns-security)
* [Web filtering](https://www.fortinet.com/support/support-services/fortiguard-security-subscriptions/web-filtering)
* [Web Application Firewall (WAF)](https://www.fortinet.com/products/web-application-firewall/fortiweb/what-is-waf)
* [SSL-VPN](https://www.fortinet.com/resources/cyberglossary/ssl-vpn)
* [IPSec VPN](https://docs.fortinet.com/document/FortiGate/7.4.3/administration-guide/520377/ipsec-vpns)
* [SD-WAN](https://www.fortinet.com/resources/cyberglossary/sd-wan-explained) and [ECMP](https://docs.fortinet.com/document/FortiGate/7.4.3/administration-guide/25967/equal-cost-multi-path)
* [Application control](https://www.fortinet.com/support/support-services/fortiguard-security-subscriptions/application-control)
* [Automation stitches](https://docs.fortinet.com/document/FortiGate/7.4.3/administration-guide/139441/automation-stitches)
* [Fortinet security fabric integration](https://www.fortinet.com/solutions/enterprise-midsize-business/security-fabric).
* [DoS policies](https://docs.fortinet.com/document/fortigate/7.4.3/administration-guide/771644/dos-policy)
* [Session Load Balancing](https://docs.fortinet.com/document/fortigate/7.4.3/administration-guide/771644/dos-policy) for distributing specific security processing like Antivirus to FortiGate cluster members in active-active HA
* [Server Load Balancing](https://docs.fortinet.com/document/fortigate/7.4.3/administration-guide/713497/virtual-server-load-balance)

For a list of known limitations with {{site.data.keyword.vfsa_full}}, see [Known limitations](/docs/vfsa?topic=vfsa-known-limitations-for-ibm-cloud-vfsa).
{: note}

## Choosing a vFSA license
{: #choosing-license}

There are three license types available for your {{site.data.keyword.vfsa_full}}:

* Advanced Threat Protection (ATP)
* Unified Threat Management (UTM)
* Enterprise

Each license includes a different set of features and options, and the following table outlines the differences.

| License Name                     | Features/Security Services                       |
|----------------------------------|--------------------------------------------------|
| Advanced Threat Protection (ATP) | Intrustion Prevention System (IPS)               |
|                                  | Application control                              |
|                                  | Geo IP Updates                                   |
|                                  | Device/OS Detection                              |
|                                  | IoT Mac Database                                 |
|                                  | Trusted Certificate Database                     |
|                                  | Internet Service (SaaS) Database                 |
|                                  | DDNS (v4/v6)                                     |
|                                  | Advanced Malware Protection (AMP)                |
|                                  | Antivirus                                        |
|                                  | Botnet                                           |
|                                  | Mobile Malware                                   |
|                                  | FortiGate Cloud Sandbox                          |
| Unified Threat Management (UTM)  | All ATP Features/Security Services               |
|                                  | Web Security                                     |
|                                  | Web and Content Filtering                        |
|                                  | Secure DNS Filtering                             |
|                                  | Video Filtering                                  |
|                                  | AntiSpam                                         |
| Enterprise                       | All UTM Features/Security Services               |
|                                  | IOT Query Service                                |
|                                  | OT Protocol Service                              |
|                                  | Security Fabric Rating and Compliance Monitoring |
|                                  | FortiConverter Service                           |
{: row-headers}
{: caption="Table 1. vFSA License Entitlements" caption-side="bottom"}
{: summary="The rows are read from left to right. The first column is the license name. The second column is a description of the features that the license enables for usage."}

You can specify your license type when you order your vFSA and can also change the license by using the [Gateway Appliance Details](/docs/vfsa?topic=vfsa-vfsa-licenses#vfsa-licenses) page.
{: note}

## Ordering a vFSA
{: #ordering-with-a-linked-account}

To order your {{site.data.keyword.vfsa_full}}, follow these steps:

1. From your browser, open the [Gateway Appliances page](/gen1/infrastructure/provision/gateway){: external} and log in to your account.

   You can also get to the page by logging in to the [IBM Cloud console](/login){: external} and selecting **Classic Infrastructure > Network > Gateway appliance**. Alternatively, from the [IBM Cloud catalog](/catalog){: external}, select the **Network** category, then choose the **Gateway Appliance** tile.

1. Choose **Fortinet** under **Vendor**.
1. Choose either **7.4.3 (up to 1 Gbps)** or **7.4.3 (up to 10 Gbps) under **Version**.
1. Choose your license type from **License**, either **UTM License** or **Enterprise License** or **ATP License**.

   See the previous section for information on the features offered with each license.
   {: tip}

1. From the **Gateway appliance** section, enter your **Hostname** and **Domain** name information. These fields are already be populated with default information, so ensure that the values are correct.
1. Check the **High Availability** option if needed, then select a data center **Location**, and the specific **Pod** you want from the menu.
1. From the **Configuration** section, choose your processor's RAM. You can also define an SSH key, if you want to use it to authenticate access to your new Gateway Ubuntu host.

   The appropriate processor is chosen for you based on the license version you selected in step 2. However, you can choose different RAM configurations.
   {: note}

1. From the **Storage disks** section, choose the options that meet your storage requirements.

   RAID configurations are available for customizing storage performance, size and data protection. RAID1 or RAID10 are recommended for a combination of data protection and performance. RAID0 provides the highest read/write performance with the least data protection. If you choose RAID0, ensure that you have a data backup plan, as one disk failure needs the complete rebuild of the RAID with new disks and the rebuilding and reloading of the appliance.

   You can have up to four disks per vFSA. "Disk size" with a RAID configuration is the usable disk size, as RAID configurations for every type of RAID other than RAID0 changes the amount of storage available.
   {: note}

1. All settings in the **Network interface** section should be preselected and are not modifiable. The vFSA supports only redundant public and private interfaces with a port speed that matches the **Version that is specified** earlier in the order form. "Private only" network interfaces are not supported.
1. Review your selections, check that you read the Third-Party Service Agreements, then click **Create**. The order is verified automatically.

After your order is approved, the provisioning of your {{site.data.keyword.vfsa_full}} Gateway starts automatically. When the provisioning process is complete, the new vFSA appears in the Gateway Appliances list page. Click the gateway name to open the Gateway Details page. The IP addresses, login username, and password for the device appear.

After you order and configure your gateway from the IBM Cloud catalog, you must also login to and configure the device itself with customized configurations for your cloud environment. This includes VLAN interfaces, subnet gateway IP addresses, security policies and any additional features that you require.
{: remember}

## Next steps
{: #what-s-next}

After your order is approved, the provisioning of your vFSA starts automatically. When the provisioning process is complete, the gateway appears in the [Gateway Appliances](/docs/gateway-appliance?topic=gateway-appliance-viewing-all-gateway-appliances) list.

Click the gateway name to open the **Gateway Details** page. You find the IP addresses, login username, and passwords for the device.
