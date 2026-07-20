---

copyright:
  years: 2026
lastupdated: "2026-07-20"

keywords: reloading, os, upgrading, kvm, ha, standalone

subcollection: vfsa

---

{{site.data.keyword.attribute-definition-list}}

# Upgrading the vFSA
{: #upgrading-the-vfsa}

Learn how to upgrade or downgrade your {{site.data.keyword.vfsa_full}}, including version requirements, supported upgrade methods, and important considerations for firmware and hypervisor maintenance.
{: shortdesc}

*	vFSA version level
*	Bare-metal server processor model
*	Bandwidth: 1 Gbps versus 10 Gbps
*	Stand-alone or High Availability (HA)

{{site.data.keyword.cloud}} **only** provisions and reload the vFSA with a certified version. The certified versions can be found in [IBM Cloud Virtual FortiGate Security Appliance supported versions](/docs/vfsa?topic=vfsa-vfsa-versions). When an OS reload or license readiness check is run, the actual version running on the vFSA is detected and stored. If this detected version matches a version in the supported version's list, then actions such as an OS reload or license update can be performed.

For vFSA deployments, the OS reload process doesn't support updating the vFSA software version. To upgrade or downgrade the vFSA version, use the FortiGate Fabric Upgrade tool. A readiness check is not required before initiating the vFSA upgrade process. Do not downgrade to a version earlier than 7.4.1. Known stability issues in earlier releases can cause HA cluster failures and might require a complete vFSA rebuild to restore service.
{: important}

The native Fortinet Fabric Upgrade tool is the primary method to upgrade and downgrade the vFSA firmware version. This tool is owned and supported by Fortinet and can be found by logging in to the FortiGate Web Console and navigating to **System > Firmware & Registration > Fabric Upgrade**. The tool lists all available upgrade and downgrade versions, and backs up the current configuration by downloading it to your local system. It automatically restores the configuration as part of the firmware version change process. Details on this tool can be found in the [FortiGate documentation](https://docs.fortinet.com/document/FortiGate/7.4.1/administration-guide/596131/upgrading-individual-device-firmware){: external}.

You can upgrade or downgrade to firmware levels not in the supported versions list. However, actions such as OS reload and license updates require you to be running a supported version. Ensure that you change your vFSA version to a supported version before you attempt these actions. If a `No valid upgrade path` error is displayed in the FortiGate Fabric Upgrade tool during the vFSA upgrade process, see [Troubleshooting Tip: No valid upgrade path error when upgrading the FortiGate firmware](https://community.fortinet.com/fortigate-3/troubleshooting-tip-no-valid-upgrade-path-error-when-upgrading-the-fortigate-firmware-219824){: external}
{: tip}

Firmware upgrades reapply the license that is currently applied to the nodes.
{: tip}

Currently, several additional considerations exist that you must be aware of:
   - Moving from one bare-metal server processor to another is not supported.
   - Moving between different bandwidth speeds (1 Gbps and 10 Gbps) is not supported.
   - Moving between Stand-alone and High Availability configurations is not supported.

## Ubuntu hypervisor upgrade considerations
{: #ubuntu-hypervisor-upgrade-considerations}

The vFSA runs as a VM on an Ubuntu hypervisor. Generally, this hypervisor operating system is reloaded as part of a vFSA update. However, in some cases, only the Ubuntu hypervisor requires maintenance, for example, to apply kernel updates, security patches, or vulnerability fixes, without upgrading the vFSA virtual machine.

In these cases, the standard `apt update` command is typically sufficient with some important caveats that you must be aware of. Upgrading only the Ubuntu hypervisor is generally considered a safe maintenance operation and can usually be performed with minimal disruption to running vFSA VMs. Most package updates, including standard user-space libraries and utilities, don't require interruption of guest VM operations.

However, administrators must carefully review the packages that are included in an upgrade before proceeding. Certain updates can affect the stability or connectivity of running virtual machines until the hypervisor is rebooted.

The following types of updates require special attention:

- Kernel packages
- `systemd` and `udev` updates
- `libvirt` packages
- Networking-related packages such as `nftables`, bridges, or other virtualization networking components
- `qemu` and `kvm` package updates

In some cases, when you upgrade virtualization or network-related packages while keeping VMs active can result in degraded VM networking, stalled interfaces, or an inconsistent `libvirt` state until the hypervisor node is restarted. A restart of the Ubuntu hypervisor typically restores normal operation.

When performing an `apt upgrade` on the hypervisor, consider the following recommendations:

- Review pending packages before applying updates.
- Schedule a maintenance window if kernel, `libvirt`, or networking components are being upgraded.
- Plan for a hypervisor reboot when required.
- Avoid performing simultaneous maintenance on multiple HA nodes when possible.

Example:

```sh
apt update
apt list --upgradable
apt upgrade
```
