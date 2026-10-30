---

copyright:
  years: 2026
lastupdated: "2026-10-30"

keywords: migration, fsa, vfsa, fortigate, firewall, gateway, cutover, vlan

subcollection: vfsa

content-type: tutorial
account-plan: paid
---

{{site.data.keyword.attribute-definition-list}}

# Migrate from FortiGate Security Appliance 10G to vFSA
{: #fsa-to-vfsa-migration}
{: toc-content-type="tutorial"}

In this tutorial, you learn how to migrate Classic IaaS workloads from a FortiGate Security Appliance (FSA) 10G to an IBM Cloud Virtual FortiGate Security Appliance (vFSA). You build the vFSA in parallel, move each VLAN during a change window, and keep rollback ready.
{: shortdesc}

This is not an in-place conversion. Each VLAN must be routed away from the FSA and then routed through the vFSA during the change window. Configure and validate the vFSA in parallel before any production traffic is moved.
{: important}

## Overview
{: #fsa-to-vfsa-migration-overview}

Use this tutorial to migrate Classic IaaS workloads from a physical FortiGate Security Appliance (FSA) 10G or FortiGate 1500DT to the Classic IBM Cloud Virtual FortiGate Security Appliance (vFSA). The goal is to ensure minimal disruption, maintain rollback capability, and preserve existing network behavior.

The migration pattern is:

1. Inventory the FSA and the protected Classic VLANs.
1. Order a correctly sized vFSA in the same pod as the VLANs to be protected.
1. Inventory and plan the cutover of any customer-managed connectivity that terminates on, peers with, or routes through the FSA: IPsec tunnels, GRE tunnels, BGP neighbors, static and dynamic routes, and paths used for Direct Link on Classic, Direct Link, Transit Gateway, on-premises, or other hybrid-cloud connectivity.
1. Build equivalent FortiOS configuration on the vFSA, with interface names remapped to the vFSA aggregate layout.
1. In a change window, move each VLAN from FSA route-through to vFSA route-through.
1. Validate traffic, VPNs, NAT, routing, logging, management, and every hybrid-connectivity adjacency.
1. Keep rollback ready until the customer signs off.

## Audience
{: #fsa-to-vfsa-audience}

This tutorial is intended for network engineers who manage a Classic FSA 10G, and for the IBM Cloud support engineers who assist with break/fix support for both the vFSA and FSA. You need working knowledge of FortiOS, Classic VLANs and subnets, and the VPN and routing protocols that your firewall uses for hybrid cloud and on-prem connectivity.

## Planning considerations
{: #fsa-to-vfsa-planning}

Review the following constraints, responsibilities, and required inputs before you plan the migration.

### Hard constraints
{: #fsa-to-vfsa-migration-constraints}

Review these constraints before planning the migration. Violations require redesign before the change window opens.

| Constraint | Why it matters |
|------------|----------------|
| Same Classic pod | FSA 10G and vFSA route Classic VLANs only in the pod where the appliance is deployed. Pod number maps to the numbered BCR and FCR. |
| One gateway association per VLAN | An FSA 10G and a network gateway cannot be associated with the same customer VLAN. Plan cutover as: remove/route-around from FSA, then associate/route-through to vFSA. |
| vFSA high availability (HA) or standalone is chosen at order time | Standalone-to-HA and HA-to-standalone conversions are not supported. Order the target shape correctly. |
| vFSA 1G/10G is chosen at order time | 1G-to-10G and 10G-to-1G conversions are not supported. Order the target speed correctly. |
| vFSA is delivered on the provisioned FortiOS version, then customer-upgradable | The FortiOS version is set when the vFSA is provisioned and delivered. After provisioning, the customer can update FortiOS to the latest supported version from the FortiGate web UI. Do not import a configuration that requires unsupported FortiOS behavior before the target is upgraded and validated. |
| Interface names and layout differ | Do not restore the whole FSA configuration blindly. Migrate objects and policy intent, then remap interfaces to vFSA aggregates. Prefer zone-based firewall policy on the target if the customer is not already using it, because zones make interface remapping easier than binding policy directly to physical or VLAN interfaces. |
| Traffic is denied by default | Firewall policies must be configured on the vFSA after VLAN interfaces are configured. |
{: caption="Hard constraints for FSA to vFSA migration" caption-side="bottom"}

### Roles and access
{: #fsa-to-vfsa-roles}

**Customer owns:**
- Business approval and maintenance window
- Application owner validation
- FortiOS policy intent: firewall rules, NAT, VPNs, routing, certificates, logging, unified threat management (UTM) and FortiGuard features
- Any secrets needed to recreate VPNs, API integrations, TACACS/RADIUS, syslog, SNMPv3, or certificates

**IBM Cloud Security Support owns or assists with:**
- Confirming product/pod limitations
- Portal route-through/route-around guidance
- FSA/vFSA platform support boundaries
- Hardware, hypervisor, and host troubleshooting support for vFSA
- FortiGate/FortiOS support and vendor escalation support when needed
- Support-engineer guidance and troubleshooting; not managed-service ownership of the customer's firewall policy, application design, or ongoing configuration management

### Secrets handling
{: #fsa-to-vfsa-secrets}

Do not paste pre-shared keys (PSKs), private keys, API tokens, SNMPv3 auth/priv secrets, TACACS/RADIUS shared secrets, or certificate private keys into a case. Enter secrets directly in FortiOS or provide a sanitized configuration with secret values removed.
{: important}

### Required inputs
{: #fsa-to-vfsa-required-inputs}

Collect the following inputs before opening the change window:

| Input | Notes |
|-------|-------|
| Source FSA gateway ID/name | Include data center, pod, HA/standalone, current firmware, serials, and management access method. |
| Target vFSA order details | HA or standalone, 1G or 10G, data center, pod, license/version. |
| VLAN list | VLAN ID, public/private, FCR/BCR/pod, subnets, gateway IPs, netmasks, and route-through state. |
| FortiOS configuration inventory | Interfaces, zones, address/service objects, firewall policies, NAT, virtual IP addresses (VIPs), IP pools, routes, BGP/OSPF, VPNs, certificates, local users/admins, logging, SNMP, syslog, FortiAnalyzer, FortiGuard add-ons. |
| Traffic matrix | Critical flows, source/destination, ports, expected NAT, expected route, validation owner. |
| Hybrid connectivity dependencies | IPsec tunnels, GRE tunnels, BGP neighbors, static routes, Direct Link on Classic, Direct Link, Transit Gateway, on-premises peers, and other cloud/provider connectivity paths that terminate on, peer with, or route through this FortiGate. |
| External dependencies | Monitoring sources, syslog/FortiAnalyzer, TACACS/RADIUS, automation/API clients. |
| Rollback decision point | Who can call rollback, by what time, and what test failure triggers it. |
{: caption="Required inputs before migration" caption-side="bottom"}

## Before you begin
{: #fsa-to-vfsa-migration-prerequisites}

Ensure the following are in place before starting:

- Change window approved
- Access to current firewall configuration
- Confirmation that the protected VLANs and the target vFSA are in the same pod and location
- Validation that IP and VLAN reuse is permitted

## Decide whether vFSA is a valid target
{: #fsa-to-vfsa-step-1}
{: step}

To decide whether vFSA is a valid target, complete the following steps:

1. Confirm every VLAN that must move is in the same pod as the intended vFSA.
1. Confirm that the required vFSA topology (HA vs. standalone) and link speed (1G vs. 10G) are finalized, as these cannot be changed after provisioning.
1. Confirm no requirement depends on physical FSA-only hardware behavior or FortiOS features unavailable on IBM-certified vFSA versions.
1. Confirm expected throughput, inspection, VPN, and logging needs fit the planned vFSA size.
1. Confirm the customer can tolerate per-VLAN route-through changes during maintenance.
1. If the FSA is part of customer/user-managed hybrid connectivity, document every IPsec tunnel, GRE tunnel, BGP neighbor, static route, Direct Link on Classic, Direct Link, Transit Gateway, on-premises, and other hybrid-cloud dependency before cutover.
1. For each hybrid connectivity dependency, define the target vFSA source interface/IP, peer change requirements, BGP autonomous system number (ASN) and session settings, route advertisements, expected outage/convergence behavior, validation test, and rollback action.

**Do not proceed if:**
- Any VLAN is in a different pod. Resolve the placement design first.
- The customer needs HA but ordered standalone. Reorder before migration.
- The customer needs 10G but ordered 1G. Reorder before migration.
- There is no rollback authority. Do not start the cutover.

## Identify the existing firewall location and inventory the source FSA
{: #fsa-to-vfsa-step-2}
{: step}

Before you order the target vFSA, locate the source FSA, collect its configuration, and identify any dependencies on its gateway IP addresses.

### Identify the existing firewall location
{: #fsa-to-vfsa-step-2-identify}

Determine the physical and logical location of the current firewall appliance, including:

- Pod
- Data center / location
- VLAN and subnet associations

This ensures the replacement device is provisioned in an equivalent environment.

### Inventory and back up the source FSA
{: #fsa-to-vfsa-step-2-inventory}

Run read-only collection from the FSA CLI. Save output to an internal, secure case or work-item location.

```text
get system status
get hardware status
get system ha status
show system interface
show firewall address
show firewall addrgrp
show firewall service custom
show firewall service group
show firewall policy
show firewall vip
show firewall ippool
show router static
show router policy
show router bgp
show router ospf
show vpn ipsec phase1-interface
show vpn ipsec phase2-interface
show vpn ssl settings
show user radius
show user tacacs+
show system admin
show log syslogd setting
show log fortianalyzer setting
show system snmp community
show system snmp user
execute backup config sftp <sanitized-backup-name> <sftp-server> <username> <password>
```

Then build the following migration planning tables.

#### VLAN/subnet table
{: #fsa-to-vfsa-vlan-table}

| VLAN | Type | Pod/FCR/BCR | Subnet | Gateway IP/mask | Source FSA interface | Target vFSA interface | Cutover status |
|------|------|-------------|--------|-----------------|---------------------|----------------------|----------------|
| `<vlan-id>` | Public or private | `<pod>` | `<cidr>` | `<gateway>` | `<old-name>` | `<new-name>` | Planned |
{: caption="VLAN/subnet inventory" caption-side="bottom"}

#### Policy/NAT table
{: #fsa-to-vfsa-policy-table}

| Source policy ID/name | Source interface | Destination interface | NAT/VIP/IP pool | Target policy action |
|----------------------|------------------|-----------------------|-----------------|----------------------|
| `<id/name>` | `<old-if>` | `<old-if>` | `<nat-object>` | Remap, create, or retire. |
{: caption="Policy/NAT inventory" caption-side="bottom"}

#### VPN/routing table
{: #fsa-to-vfsa-vpn-routing-table}

| Feature | Peer/neighbor | Source interface | Target interface | Cutover action |
|---------|--------------|-----------------|-----------------|----------------|
| IPsec/BGP/OSPF/static | `<peer>` | `<old-if>` | `<new-if>` | Recreate, test, and disable old. |
{: caption="VPN/routing inventory" caption-side="bottom"}

#### Hybrid connectivity planning table
{: #fsa-to-vfsa-hybrid-table}

FortiGates often act as customer-managed network endpoints, not just VLAN firewalls. Treat these dependencies as first-class migration items. A missed IPsec, GRE, or BGP dependency can break on-premises, Direct Link on Classic, Direct Link, Transit Gateway, or other hybrid/cloud connectivity even when the local VLAN cutover looks clean.
{: important}

| Dependency | Current FSA endpoint | Target vFSA endpoint | Peer/customer change required | Routes/advertisements | Validation | Rollback |
|------------|---------------------|---------------------|------------------------------|-----------------------|------------|---------|
| IPsec tunnel | `<old-public-ip/interface>` | `<new-public-ip/interface>` | Update peer IP, PSK, certificates, and proposals if needed. | `<local/remote selectors or routed VPN routes>` | `get vpn ike gateway`; app or on-premises test | Move peer back to FSA endpoint. |
| GRE tunnel | `<old-interface/ip>` | `<new-interface/ip>` | Update remote tunnel endpoint if needed. | `<routes over GRE>` | Tunnel status and route reachability | Restore old tunnel endpoint and routes. |
| BGP neighbor | `<old-source-ip/asn>` | `<new-source-ip/asn>` | Update neighbor source and peer allowlists if needed. | `<prefixes advertised/received>` | `get router info bgp summary`; route table | Restore old neighbor and session. |
| Direct Link on Classic / Direct Link path | `<current FortiGate path>` | `<target FortiGate path>` | Coordinate route or peer changes. | `<prefixes>` | On-premises/cloud flow test | Revert routes and peer configuration. |
| Transit Gateway path | `<current FortiGate path>` | `<target FortiGate path>` | Coordinate Transit Gateway route expectations. | `<prefixes>` | Transit Gateway reachability and app test | Revert routes and peer configuration. |
{: caption="Hybrid connectivity planning" caption-side="bottom"}

IPsec between an FSA and a vFSA is supported, but cipher choice matters. Do not assume that AES-GCM improves throughput on NP6-era FortiGate hardware. Test throughput with the proposals that you plan to use.
{: note}

### Review the existing firewall configuration
{: #fsa-to-vfsa-step-2-review}

Examine the current firewall configuration to identify any dependencies on gateway IP addresses. Pay special attention to services such as:

- IPsec tunnels
- GRE tunnels
- BGP or other dynamic routing protocols

**Outcomes:**

- **If gateway IPs are in use:** Document each dependency in the hybrid connectivity planning table and resolve it before proceeding. Peer-side changes are the customer's responsibility. To avoid coordinating every peer during the change window, see [Move gateway IP dependencies to a static subnet](#fsa-to-vfsa-static-subnet).
- **If gateway IPs are not in use:** Proceed with migration planning and review [Configuring your VLAN interfaces and subnets](/docs/vfsa?topic=vfsa-configure-vlan-subnets) to confirm proper routing behavior.

### Move gateway IP dependencies to a static subnet
{: #fsa-to-vfsa-static-subnet}

The gateway IP addresses of a Classic gateway device can't be moved to a different device. These two gateway IPs, one public and one private, are different from the subnet gateway IPs that you configure on VLAN interfaces. They are tied to the gateway object, not to the hardware. When you associate and route a VLAN to a gateway, the subnets on that VLAN are statically routed to the gateway IP. If you delete the old gateway, its IP addresses are released. When you migrate from an FSA to a vFSA, the vFSA receives different gateway IPs.

Services that use the gateway IPs as endpoints, such as IPsec tunnels and BGP neighbors, are affected. If you have only one or two peers, you can update them during the change window. If you have five or more, it can be impractical to schedule all of the peer owners for the same change window. You also can't migrate some peers now and the rest later.

To avoid this problem, move these endpoints to a static subnet before the change window. A static subnet is routed to the gateway IP, and you can reroute it to the new gateway during cutover.

Complete the following steps before the change window:

1. Identify all services that use the FSA gateway IP addresses as endpoints. IPsec tunnels and BGP neighbors are the most common, but verify whether other services also use them.
1. [Order a static subnet](/docs/subnets?topic=subnets-order-subnets). When you are prompted for the IP address to route the subnet to, enter the public or private gateway IP of the FSA, depending on whether you ordered a public or private subnet.
1. After the subnet is provisioned, configure IP addresses from the subnet on the FSA interfaces. Use a public subnet for public endpoints and a private subnet for private endpoints.
1. Contact each remote location and ask them to change the peer IP address that they use to an IP address from the new static subnet. Services now use the static subnet IPs instead of the gateway IPs.
1. On the vFSA, configure each service exactly as it is configured on the FSA, by using the same static subnet IPs. Because the subnet is still routed to the FSA, these services are configured but can't communicate until you reroute the subnet. Place the subnet IPs on the vFSA interfaces that match the traffic class in the [vFSA interface map](#fsa-to-vfsa-interface-map).

During the change window, complete the following steps:

1. [Reroute the static subnet](/docs/subnets?topic=subnets-re-routing-secondary-subnets) to the vFSA. Traffic for the subnet starts to flow to the vFSA. If the vFSA is configured correctly, BGP adjusts routes and IPsec tunnels are re-established.
1. Detach the VLANs from the FSA and route them to the vFSA, as described in [Execute the VLAN cutover](#fsa-to-vfsa-step-6).
1. Test everything to verify that it works.

Static subnet IPs are routed to a gateway IP, so they remain valid across the cutover. You must coordinate with each peer only once, before the change window.
{: note}

## Order and prepare the target vFSA
{: #fsa-to-vfsa-step-3}
{: step}

Configure the new firewall to mirror the existing device as closely as possible before migration, including:

- Identical subnet gateway IP addresses and VLAN assignments for the protected VLANs.
- Equivalent firewall policies, NAT rules, and routing configuration.
- IPsec, GRE, and BGP endpoints as defined in the [hybrid connectivity planning table](#fsa-to-vfsa-hybrid-table). These endpoints might differ from the FSA.

This step helps reduce disruption during cutover.

Complete the following preparation steps:

1. Order the vFSA from the IBM Cloud catalog with the correct fixed properties:
   - HA or standalone
   - 1G or 10G
   - Same physical or logical location, data center, and pod as the VLANs to protect
   - Compatible throughput and feature set
   - Public/private redundant interfaces as provided by the order form
1. Wait for provisioning to complete.
1. Record target details: gateway ID, delivered FortiOS version, license state, management URL/IP, HA state, and transit VLANs.
1. After the vFSA is provisioned, update FortiOS to the latest supported version through the FortiGate web UI, unless there is an approved reason to stay on the delivered version.
1. Back up the delivered configuration before and after the FortiOS update.
1. Run the [vFSA readiness check](/docs/vfsa?topic=vfsa-vfsa-readiness) in the IBM Cloud console for supported actions and resolve any Not Ready state before migration.
1. Confirm FortiOS access with least-privilege accounts.
1. Configure baseline management controls:
   - Admin accounts and TACACS or RADIUS
   - Trusted hosts, if used
   - Logging, syslog, and FortiAnalyzer
   - SNMPv3, if used
   - NTP, DNS, and FortiGuard reachability
1. Back up the fresh vFSA configuration before making migration changes.

## Translate the FSA configuration to vFSA interfaces
{: #fsa-to-vfsa-step-4}
{: step}

Do not import the complete FSA backup configuration without review from a subject matter expert (SME) for interface and platform differences. Prefer a staged, auditable translation.

If the source FSA uses interface-bound firewall policies, strongly consider converting the target vFSA design to zone-based firewall policy during the migration. Zones make the FSA-to-vFSA interface remap cleaner and reduce policy churn during later VLAN/interface changes.

### vFSA interface map
{: #fsa-to-vfsa-interface-map}

**For HA vFSA:**

| Traffic class | Parent interface |
|---------------|-----------------|
| Outside/private transit | agg0 |
| Outside/public transit | agg1 |
| Inside/private customer VLANs | agg2 |
| Inside/public customer VLANs | agg3 |
{: caption="HA vFSA interface map" caption-side="bottom"}

**For standalone vFSA:**

| Traffic class | Parent interface |
|---------------|-----------------|
| Inside/private customer VLANs | agg0 |
| Inside/public customer VLANs | agg1 |
{: caption="Standalone vFSA interface map" caption-side="bottom"}

The [vFSA VLAN and subnet configuration documentation](/docs/vfsa?topic=vfsa-configure-vlan-subnets) is the authoritative reference for the target interface mapping.

### Example 1: Private VLAN interface on HA vFSA
{: #fsa-to-vfsa-private-vlan-example}

```text
config system interface
    edit "v<VLAN>-b-inside"
        set vdom "root"
        set ip <subnet-gateway-ip> <subnet-mask>
        set allowaccess ping
        set role lan
        set interface "agg2"
        set vlanid <VLAN>
    next
end
```

### Example 2: Public VLAN interface on HA vFSA
{: #fsa-to-vfsa-public-vlan-example}

```text
config system interface
    edit "v<VLAN>-f-inside"
        set vdom "root"
        set ip <subnet-gateway-ip> <subnet-mask>
        set allowaccess ping
        set role lan
        set interface "agg3"
        set vlanid <VLAN>
    next
end
```

### Example 3: Secondary subnet gateway on the same VLAN
{: #fsa-to-vfsa-secondary-subnet-example}

```text
config system interface
    edit "v<VLAN>-b-inside"
        set secondary-IP enable
        config secondaryip
            edit 0
                set ip <additional-gateway-ip> <additional-subnet-mask>
                set allowaccess ping
            next
        end
    next
end
```

### Configuration recreation order
{: #fsa-to-vfsa-config-order}

After interfaces exist, recreate or import in this order:

1. Address/service objects and groups.
1. Static routes and dynamic routing base configuration, with interfaces remapped.
1. IP pools, VIPs, central SNAT, and NAT settings.
1. Firewall policies, with interfaces/zones remapped.
1. VPN phase 1 and phase 2, with PSKs/certs entered securely by the customer.
1. Logging, SNMP, automation/API access, and admin auth.
1. FortiGuard/UTM profiles only after license and service reachability are confirmed.

## Validate the vFSA before cutover
{: #fsa-to-vfsa-step-5}
{: step}

Validate configuration on the vFSA while production VLANs are still routed through the FSA. Do not route production VLANs through the vFSA yet.

```text
get system status
get system ha status
diagnose hardware deviceinfo nic
diagnose netlink aggregate name agg0
diagnose netlink aggregate name agg1
diagnose netlink aggregate name agg2
diagnose netlink aggregate name agg3
show system interface
show firewall policy
get router info routing-table all
get vpn ike gateway
```

Verify the following expected results before proceeding:

| Check | Expected result |
|-------|----------------|
| FortiOS version/license | Latest supported vFSA FortiOS version after web UI update unless a documented exception exists; licensed, no trial/expired state. |
| HA state (if HA) | Expected primary/secondary state, no split brain, no unexpected failover loop. |
| VLAN interfaces | Every planned VLAN has the correct VLAN ID, parent aggregate, gateway IP, and secondary IPs. |
| Policies | Every critical flow has a target policy with the correct NAT behavior. |
| Routes | Static/dynamic route configuration references target interfaces and expected next hops. |
| VPNs | Configuration staged, but peers might remain down until route-through/DNS/peer changes happen. |
| Logging | Logs visible locally and sent to the configured external destination if used. |
{: caption="Pre-cutover validation expected results" caption-side="bottom"}

## Execute the VLAN cutover
{: #fsa-to-vfsa-step-6}
{: step}

Move the protected VLANs from the FSA to the vFSA during the approved change window, and validate each batch before you continue.

### Move the VLANs in batches
{: #fsa-to-vfsa-migration-window}

Cut over in small batches unless the customer explicitly accepts an all-at-once outage. A public/private VLAN pair for the same application usually moves together.

For each batch:

1. Start a bridge call or active case update with the customer/application owner.
1. Record current FSA route-through state and active sessions.
1. Confirm rollback owner and deadline.
1. In the IBM Cloud console for the **source FSA**:
   - Detach (route around) the VLAN from the existing firewall gateway.
   - Disassociate the VLAN from the FSA if required by the portal flow.
1. In the IBM Cloud console for the **target vFSA/gateway appliance**:
   - Attach (associate) the same VLANs to the new firewall gateway.
   - Set the VLAN to route through the vFSA.
1. If required, update static routes to point to the appropriate gateway IPs.
1. On the vFSA, confirm interface and route visibility.
1. Test gateway reachability from at least one host on the moved VLAN.
1. Test critical flows from the traffic matrix.
1. Test external dependencies: VPNs, BGP/OSPF, monitoring, syslog, FortiAnalyzer, and NAT.
1. Watch FortiGate logs during validation:

```text
diagnose debug flow filter clear
diagnose debug flow filter addr <test-source-or-destination>
diagnose debug flow show function-name enable
diagnose debug enable
diagnose debug flow trace start 100
```

Stop debug after the test:

```text
diagnose debug disable
diagnose debug flow filter clear
```

### Validate each VLAN batch
{: #fsa-to-vfsa-cutover-validation}

Confirm the following after each VLAN batch:

- Confirm traffic is flowing through the new firewall.
- Verify connectivity for critical workloads.
- Validate routing, VPNs, and any dependent services.

Use the following table to track detailed test results for each batch:

| Test | Command/source | Expected | Pass/fail | Notes |
|------|---------------|----------|-----------|-------|
| Default gateway ping | Host behind moved VLAN | Gateway IP replies if ping allowed | | |
| East/west traffic | App owner test | Expected policy allows flow | | |
| Internet/NAT | Host curl/dig/ping as appropriate | Expected egress IP/NAT | | |
| Inbound VIP | External test source | Expected service reachable | | |
| VPN | `get vpn ike gateway` / peer test | Phase 1 and 2 established | | |
| BGP | `get router info bgp summary` | Neighbor established, routes learned/advertised | | |
| Logs | FortiOS/log destination | Expected allow/deny logs visible | | |
{: caption="Cutover validation" caption-side="bottom"}

## Harden the vFSA and clean up after cutover
{: #fsa-to-vfsa-step-7}
{: step}

After all VLANs have moved and the customer has validated the results, complete the following steps:

1. Take a new vFSA configuration backup.
1. Confirm no production VLANs remain associated or routed through the FSA.
1. Confirm monitoring points to the vFSA and old FSA alerts are suppressed or retired.
1. Confirm logging/FortiAnalyzer/syslog is receiving from the vFSA.
1. Confirm VPN peers, BGP neighbors, and automation clients no longer reference old FSA addresses unless intentionally retained.
1. Monitor traffic and logs for anomalies.
1. Document the migration outcome.
1. Keep the FSA for the monitoring period that you and the customer agreed on. During this time, the vFSA carries production traffic while you monitor it for stability.
1. After the monitoring period ends and the customer gives explicit approval, cancel or decommission the old FSA.
1. Attach sanitized final evidence to the case.

Schedule decommissioning of old hardware once stability is confirmed (if applicable).
{: tip}

## Roll back a failed VLAN batch
{: #fsa-to-vfsa-rollback}

Use this procedure if a VLAN batch fails validation after you move it to the vFSA. The rollback triggers that follow the steps list the conditions that call for a rollback.

Keep the FSA configuration untouched until migration is complete. The original firewall should remain available for disaster recovery purposes. Rollback is a portal routing change.

To roll back a failed VLAN batch:

1. Stop making changes.
1. Capture failure evidence: which VLANs moved, which tests failed, FortiOS logs/debug output (sanitized), and route/VPN/BGP state.
1. In the IBM Cloud console for the **vFSA**:
   - Detach (route around) the VLAN from the new firewall.
   - Disassociate the VLAN from the vFSA if required.
1. In the IBM Cloud console for the **FSA**:
   - Reattach (re-associate) the VLAN to the original firewall.
   - Route the VLAN through the FSA.
1. Restore any static routing changes, if applicable.
1. Validate the same tests against the original path.
1. Leave the vFSA configuration in place for analysis unless it is actively harmful.
1. Do not retry until the root cause is identified.

This process effectively reverses the migration and restores the previous state.

**Rollback triggers:**
- Critical application test fails and cannot be fixed inside the window.
- VPN/BGP adjacency fails and blocks production traffic.
- NAT/VIP behavior differs from the approved traffic matrix.
- Unexpected broad deny/drop behavior appears in FortiOS flow debug.
- HA instability, host/hypervisor problems, or repeated failovers.

## Migration support materials
{: #fsa-to-vfsa-support-materials}

Use the following templates and checklists to document the migration, communicate with the customer, decide when to escalate, and confirm that the migration is complete.

### Evidence template
{: #fsa-to-vfsa-evidence-template}

Attach sanitized final evidence to the case using the following template.

```text
Migration: FSA 10G / FortiGate 1500DT to vFSA
Date/time UTC:
Customer/account:
Source FSA gateway ID/name:
Target vFSA gateway ID/name:
Data center / pod:
Target shape: standalone or HA, 1G or 10G
FortiOS source version:
FortiOS target version:

VLANs moved:
- VLAN <id> <public/private> <subnets> moved at <time>; validation <pass/fail>

Major configuration areas migrated:
- Interfaces:
- Firewall policies:
- NAT/VIP/IP pools:
- Static/dynamic routing:
- VPN:
- Logging/SNMP/FortiAnalyzer:
- Admin/TACACS/RADIUS:

Validation summary:
- Gateway reachability:
- Critical app flows:
- NAT/VIP:
- VPN/BGP/OSPF:
- Logs/monitoring:

Rollback readiness:
- Last known good FSA backup:
- Rollback owner:
- Rollback deadline:

Issues / follow-up:
```

### Escalation criteria
{: #fsa-to-vfsa-escalation}

**Escalate to FortiGate/Fortinet SME when:**
- A FortiOS configuration translation is unclear or a feature behaves differently on the vFSA certified version.
- IPsec/BGP behavior changes after interface remap and standard flow debug does not explain it.
- UTM/FortiGuard licensing or service reachability blocks production policy behavior.

**Escalate to platform/network underlay owners when:**
- The vFSA readiness check is Not Ready or Incomplete and remediation is not obvious.
- The vFSA host, hypervisor, disk, aggregate interface, or HA dataplane appears unhealthy.
- Portal route-through/route-around state does not match dataplane behavior.
- VLANs are not in the expected pod or cannot be associated/routed as designed.

**Escalate to customer/application owner when:**
- Approved traffic matrix is incomplete.
- A business test fails but FortiOS/network evidence shows expected policy behavior.
- The maintenance window must be extended or rollback decision is needed.

### Final checklist
{: #fsa-to-vfsa-checklist}

- [ ] Target vFSA ordered with correct HA/standalone and 1G/10G shape.
- [ ] Target vFSA in same pod as protected VLANs.
- [ ] Target vFSA updated from the delivered FortiOS version to the latest supported version through the FortiGate web UI, unless an exception is documented.
- [ ] Source FSA configuration backed up and sanitized inventory complete.
- [ ] vFSA readiness check passed or exceptions documented.
- [ ] VLAN interfaces and subnet gateway IPs configured on vFSA before route-through.
- [ ] Zone-based firewall policy considered or implemented on the vFSA target design.
- [ ] Policies/NAT/VIPs/routes/VPNs remapped to vFSA interfaces.
- [ ] Customer traffic matrix approved.
- [ ] Rollback plan approved and old FSA left intact.
- [ ] VLAN cutover completed in portal.
- [ ] Post-cutover tests passed.
- [ ] vFSA configuration backed up after migration.
- [ ] Old FSA retained through the agreed monitoring period and canceled only after explicit approval.

## Summary
{: #fsa-to-vfsa-summary}

You migrated your Classic FortiGate Security Appliance 10G to a Classic vFSA. You built the vFSA in parallel, recreated the approved FortiOS configuration with the vFSA interface layout, and moved the protected VLANs in batches during the change window. You validated traffic, VPNs, NAT, routing, and logging after each batch, and you kept the rollback procedure available until sign-off.

Keep the old FSA through the agreed monitoring period. Cancel it only after the customer gives explicit approval.

## Related links
{: #fsa-to-vfsa-related}

- [About IBM Cloud Virtual FortiGate Security Appliance](/docs/vfsa?topic=vfsa-about-ibm-cloud-vfsa)
- [Understanding the vFSA default configuration](/docs/vfsa?topic=vfsa-understanding-the-vfsa-default-configuration)
- [Importing and exporting a vFSA configuration](/docs/vfsa?topic=vfsa-importing-exporting-vfsa-configuration)
- [Known limitations](/docs/vfsa?topic=vfsa-known-limitations-for-ibm-cloud-vfsa)
- [About FortiGate Security Appliance 10 Gbps](/docs/fortigate-10g?topic=fortigate-10g-about-fortigate-security-appliance-10gbps)
- [Updating the FSA 10 Gbps firmware](/docs/fortigate-10g?topic=fortigate-10g-updating-fsa-10g)
