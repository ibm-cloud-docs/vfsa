---

copyright:
  years: 2017, 2026
lastupdated: "2026-11-10"

keywords: bypass, bypassing, firewall, rules, enable, enabling, raci, shared responsibilities, customer responsibilities, IBM responsibilities, vFSA

subcollection: vfsa

---

{{site.data.keyword.attribute-definition-list}}

# Roles and responsibilities for IBM Cloud gateways and firewalls
{: #ga-raci}

## Overview of shared responsibilities
{: #ga-raci-overview}

IBM Cloud provides several [Gateway Appliances](/docs/gateway-appliance?topic=gateway-appliance-getting-started-ga) and [firewall offerings](/docs/fortigate-10g?topic=fortigate-10g-exploring-firewalls). These offerings are not managed services. As a result, it's important that you understand the shared responsibilities between the client (or their managed services provider) and IBM when you use these services. The following sections detail these responsibilities by using the following guidelines.
{: shortdesc}

Responsible (R)
: Has the duty and the obligation to do the work. Also, has a duty to exercise independent judgement to raise appropriate issues.

Accountable (A)
: Has the authority to decide, and is the recipient of, any consequences (there can be only one "A" per-process step).

Consulted (C)
: Must be allowed to influence plans and decisions before finalization by the "Responsible" party.

Informed (I)
: Is informed of progress, key decisions, and deliverables by the "Responsible" party.

Order (O)
: Has the ability to place an order.

## Monitoring
{: #ga-raci-monitoring}

The client (or their managed services provider) is responsible for monitoring the performance, connectivity, and hardware of their devices. IBM Cloud does not monitor individual customer devices, their configurations, or network status or assignments. Should issues arise, you can open a [support ticket](/docs/gateway-appliance?topic=gateway-appliance-getting-help) to schedule hardware replacement.

| Activity | Client | IBM |
| -------- | ------ | --- |
| Supervision and technical monitoring of devices (gateways, firewalls, bare metal / virtual servers) | R, A | C, I |
| Supervision and technical monitoring of hardware infrastructure outside of the direct customer environment | C, I | R, A |
| Customer hardware and component monitoring | R, A | C, I |
{: caption="Monitoring" caption-side="top"}

## MRO (Maintenance, repair, and operations)
{: #ga-raci-mro}
The client (or their managed services provider) is responsible for ongoing maintenance and operation of their devices including firmware and operating system upgrades (for example, VyOS and Juniper). This includes monitoring security advisories from the device vendor and applying security patches and updates in a timely manner. For the IBM Cloud Virtual FortiGate Security Appliance (vFSA), IBM Cloud sends notifications about critical Fortinet security issues, but you remain responsible for monitoring vendor advisories and for applying patches. IBM Cloud does not apply patches to customer devices.
The client team provides all relevant details regarding initial troubleshooting that has been completed and might request additional assistance by using a [support ticket](/docs/gateway-appliance?topic=gateway-appliance-getting-help), live chat, or phone call to IBM Cloud technical support.

| Activity | Client | IBM |
| -------- | ------ | --- |
| General maintenance of devices in customer infrastructure | R, A | C, I |
| Monitor vendor security advisories and assess vulnerabilities that affect devices and underlying operating systems | R, A | C, I |
| Apply firmware, operating system, and security patches to devices and underlying hosts | R, A | C, I |
| Disaster recovery and high availability testing | R, A | C, I |
| Regularly scheduled device backups | R, A | C, I |
| Initial operating system setup and configuration (pre-provision and handoff to client) | C, I | R, A |
| Hardware maintenance and replacement | C, I, O | R, A |
| Break/fix support | C, I | R, A |
{: caption="MRO (Maintenance, repair, and operations)" caption-side="top"}

## Administration (ongoing device management)
{: #ga-raci-admin}

The client (or their managed services provider) is responsible for the ongoing administration of their environment, devices, user accounts, and so on. IBM Cloud technical support is available to provide guidance, answer questions, and escalation to internal teams or vendors for complex issues that require additional assistance. IBM Cloud technical support does not perform migrations. Our technical staff is always available for assistance should an issue occur during a migration event/window. However, we will not be able to join the entirety of a scheduled migration event call.

| Activity | Client | IBM |
| -------- | ------ | --- |
| Configuration and management of services (post-provision) | R, A | C, I |
| Configuration and management of firewalls, gateway devices, and underlying hosts (where applicable, post-provision) | R, A | C, I |
| Planning and preparation for change requests on firewall and gateway devices (configuration changes, operating system or firmware updates) | R, A | C, I |
| Perform change requests on firewall and gateway devices (configuration changes, operating system or firmware updates) | R, A | C, I |
| Manage IP address assignments (post provision) | R, A, O | C, I |
| Manage VLAN associations (post provision) | R, A, O | C, I |
| Configuration and management of IPSec/GRE tunnels to remote client environment | R, A | C, I |
| Migrations - planning, preparation, implementation - moving from one solution to another | R, A | C, I |
{: caption="Administration (ongoing device management)" caption-side="top"}

## LCM (Life Cycle Management)
{: #ga-raci-lcm}

When a device or operating system reaches its End of Support (EOS) or End of Life (EOL), IBM Cloud no longer can provide support for it. After the device is upgraded to a supported firmware or operating system, support can resume on the device.

| Activity | Client | IBM |
| -------- | ------ | --- |
| Upgrade an operating system that has reached EOS/EOL | R, A, O | C, I |
| Upgrade the hardware of a device | R, A, O | C, I |
| Support of devices after EOS/EOL | R, A | |
{: caption="LCM (Life Cycle Management)" caption-side="top"}

## Additional notes and scope of support
{: #raci-notes}

For Consulting (C) and Informing (I), IBM Cloud technical support occurs through [support tickets](/docs/gateway-appliance?topic=gateway-appliance-getting-help), live chats, or phone calls. It is expected that the client (or their managed services provider) provides technical resources with a strong understanding of the environment to aid with troubleshooting issues. IBM Cloud technical support is available to augment the client's own technical team and provide guidance and support as needed, bringing in any relevant internal team or vendor for additional assistance if warranted. Any break/fix support provided by IBM Cloud technical support (beyond restoring a device to its original working configuration) is done on a "best effort" basis.

## Related links
{: #ga-raci-related-links}

- [Managing firmware updates and vulnerability patches](/docs/vfsa?topic=vfsa-managing-vulnerabilities)
