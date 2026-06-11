---

copyright:
  years: 2026
lastupdated: "2026-06-11"

keywords: reloading, os, upgrading, kvm, ha, standalone

subcollection: vfsa

---

{{site.data.keyword.attribute-definition-list}}

# Upgrading the vFSA
{: #upgrading-the-vfsa}

There are several considerations to understand before you upgrade your {{site.data.keyword.vfsa_full}}:
{: shortdesc}

*	vFSA version level
*	Bare-metal server processor model
*	Bandwidth: 1 Gbps versus 10 Gbps
*	Stand-alone or High Availability (HA)

{{site.data.keyword.cloud}} will **only** provision and reload the vFSA with a certified version. The certified versions can be found in [IBM Cloud Virtual FortiGate Security Appliance supported versions](/docs/vfsa?topic=vfsa-vfsa-versions). When an OS reload or license readiness check is run, the actual version running on the vFSA is detected and stored. If this detected version matches a version in the supported version's list, then actions such as an OS reload or license update can be performed.

The native Fortinet Fabric Upgrade tool is the primary method to upgrade and downgrade the vFSA firmware version. This tool is owned and supported by Fortinet and can be found by logging in to the FortiGate Web Console and navigating to **System > Firmware & Registration > Fabric Upgrade**. The tool lists all available upgrade and downgrade versions, and backs up the current configuration by downloading it to your local system. It automatically restores the configuration as part of the firmware version change process. Details on this tool can be found in the [FortiGate documentation](https://docs.fortinet.com/document/FortiGate/7.4.1/administration-guide/596131/upgrading-individual-device-firmware){: external}.

Never downgrade to a version older than 7.4.1. There are known stability issues that break the HA cluster and make it difficult to recover the vFSA without a full rebuild.
{: important}

You can upgrade or downgrade to firmware levels not in the supported versions list. However, actions such as OS reload and license updates require you to be running a supported version. Ensure that you change your vFSA version to a supported version before you attempt these actions.
{: tip}

Firmware upgrades reapply the license that is currently applied to the nodes.
{: tip}

Currently, there are several additional considerations to be aware of:
   - Moving from one bare-metal server processor to another is not supported.
   - Moving between different bandwidth speeds (1 Gbps and 10 Gbps) is not supported.
   - Moving between Stand-alone and High Availability configurations is not supported.

## Ubuntu hypervisor upgrade considerations
{: #ubuntu-hypervisor-upgrade-considerations}

The vFSA runs as a VM on a Ubuntu hypervisor. Generally, this hypervisor operating system is reloaded as part of a vFSA update.  However, there are times where only the Ubuntu hypervisor requires maintenance, such as applying kernel updates, security patches, or vulnerability fixes, without upgrading the vSRX virtual machine itself. 

In these cases, the standard `apt update` command is typically sufficient with some important caveats that you must be aware of. Upgrading only the Ubuntu hypervisor is generally considered a safe maintenance operation and can usually be performed with minimal disruption to running vSRX VMs. Most package updates, including standard user-space libraries and utilities, don't require interruption of guest VM operations.

However, administrators must carefully review the packages included in an upgrade before proceeding. Certain updates can affect the stability or connectivity of running virtual machines until the hypervisor is rebooted. 

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
