---

copyright:
  years: 2026
lastupdated: "2026-10-05"

keywords: reloading, os, upgrading, kvm, ha, standalone

subcollection: vfsa

---

{{site.data.keyword.attribute-definition-list}}

# Upgrading the vFSA
{: #upgrading-the-vfsa}

Learn how to update the FortiOS firmware and Ubuntu hypervisor on your {{site.data.keyword.vfsa_full}}, including version requirements, supported maintenance methods, and important upgrade considerations.
{: shortdesc}

A {{site.data.keyword.vfsa_full}} has two primary software components that require maintenance:

- The FortiGate VM running FortiOS
- The Ubuntu KVM hypervisor hosting the FortiGate VM

FortiOS and Ubuntu are maintained differently. Use the FortiGate management interface to upgrade or downgrade FortiOS. Use an IBM Cloud OS reload when moving the Ubuntu hypervisor to another supported major LTS release.

IBM Cloud provisions and reloads vFSAs only with qualified combinations of FortiOS and Ubuntu. These combinations are listed in [IBM Cloud Virtual FortiGate Security Appliance supported versions](/docs/vfsa?topic=vfsa-vfsa-versions).

When you run an OS Reload or license readiness check, IBM Cloud detects the FortiOS version currently running on the vFSA. Actions such as an OS reload or license update require the detected FortiOS version to match a version that is qualified for the requested operation.

## Updating FortiOS
{: #updating-fortios}

Do not use an IBM Cloud OS reload only to upgrade or downgrade FortiOS.

To change the FortiOS version, use the FortiGate firmware upgrade tools in the FortiGate web interface. If the vFSA is managed by FortiManager, you can update the firmware through the FortiManager web interface instead.

A readiness check is not required before you upgrade or downgrade a FortiOS firmware.

A vFSA can and must run a FortiOS release that is newer than the versions currently available for IBM Cloud provisioning or OS reload operations. IBM Cloud does not qualify for every FortiOS maintenance release for provisioning and reload operations.

Before you reload the OS reload or update the license, ensure that you understand that the vFSA needs to be running a FortiOS version that is listed in [IBM Cloud Virtual FortiGate Security Appliance supported versions](/docs/vfsa?topic=vfsa-vfsa-versions) during the maintenance to update the Ubuntu LTS version.

Do not downgrade to a FortiOS version earlier than 7.4.1. Known stability issues in earlier releases can cause HA cluster failures and might require a complete vFSA rebuild to restore service.
{: important}

To update FortiOS from the FortiGate web interface, go to **System > Firmware & Registration** and use the available firmware upgrade options. Follow the Fortinet-supported upgrade path for the source and target FortiOS releases.

Before changing FortiOS versions, create a backup of the current configuration. Downgrading FortiOS can introduce configuration compatibility issues because configuration that is created by a newer FortiOS release might not be supported by an older release. For more information, see the [FortiGate firmware upgrade documentation](https://docs.fortinet.com/document/FortiGate/7.4.1/administration-guide/596131/upgrading-individual-device-firmware){: external}.

If a `No valid upgrade path` error is displayed during a FortiOS upgrade, see [Troubleshooting Tip: No valid upgrade path error when upgrading the FortiGate firmware](https://community.fortinet.com/fortigate-3/troubleshooting-tip-no-valid-upgrade-path-error-when-upgrading-the-fortigate-firmware-219824){: external}.
{: tip}

## Updating the Ubuntu hypervisor
{: #updating-the-ubuntu-hypervisor}

The recommended method for moving the vFSA hypervisor to another major Ubuntu LTS release is an IBM Cloud OS reload.

Although Ubuntu supports in-place release upgrades, such as upgrading from Ubuntu 22.04 to Ubuntu 24.04 by using `do-release-upgrade`, an OS reload provides a clean installation based on an IBM-qualified Ubuntu and FortiOS combination. This approach reduces the risk of issues that are caused by package changes, obsolete dependencies, networking configuration changes, or virtualization components that are carried forward from the previous Ubuntu release.

Customers who choose to upgrade an existing Ubuntu release in place must understand that the resulting software state might differ from the Ubuntu image that IBM Cloud provisions and validates for the vFSA.

Before starting an OS reload, the FortiGate must be running a FortiOS version that is qualified for the target Ubuntu release. The available combinations are listed in [IBM Cloud Virtual FortiGate Security Appliance supported versions](/docs/vfsa?topic=vfsa-vfsa-versions).

For example, assume the following environment:

- Current Ubuntu version: 20.04 or 22.04
- Current FortiOS version: 7.4.12
- Target Ubuntu version: 24.04
- IBM-qualified FortiOS version for Ubuntu 24.04: 7.4.11

For an HA vFSA, use the following process:

1. From the FortiGate web interface, downgrade the HA cluster from FortiOS 7.4.12 to FortiOS 7.4.11.
2. Verify that the cluster is healthy after the FortiOS downgrade.
3. Run the IBM Cloud OS Reload readiness check.
4. Reload the first vFSA node with Ubuntu 24.04 and FortiOS 7.4.11.
5. Verify that the reloaded node returns to service and that HA is healthy.
6. Run the OS Reload readiness check again.
7. Reload the second vFSA node with Ubuntu 24.04 and FortiOS 7.4.11.
8. Verify that both nodes are healthy and running Ubuntu 24.04 with FortiOS 7.4.11.
9. From the FortiGate web UI, upgrade the HA cluster from FortiOS 7.4.11 back to FortiOS 7.4.12.
10. Verify HA status, interfaces, routing, and traffic flow.

After the OS reload is complete, the FortiGate can be upgraded to a newer FortiOS maintenance release by using the FortiGate firmware upgrade tools.
{: tip}

## Unsupported vFSA changes
{: #unsupported-vfsa-changes}

The following changes are not available as part of a vFSA upgrade or OS reload:

- Moving the vFSA to a different bare-metal server processor model
- Changing between 1 Gbps and 10 Gbps configurations
- Changing between stand-alone and High Availability (HA) configurations

## Ubuntu hypervisor maintenance
{: #ubuntu-hypervisor-maintenance}

The Ubuntu hypervisor also requires routine package, kernel, and security updates between OS reloads.

Use the standard Ubuntu package-management process for these updates:

```sh
sudo apt update
apt list --upgradable
sudo apt upgrade
```

`apt update` refreshes the available package information. `apt upgrade` installs the available package updates.

Review the pending package changes before applying them, especially when the update includes components that can affect virtualization or networking.

Pay particular attention to updates that involves the following components:

- Linux kernel packages
- `systemd` or `udev`
- `libvirt`
- `qemu` or KVM components
- Network interfaces, bridges, or related networking packages
- `nftables` or other packet-processing components

Most user-space package updates do not require the running FortiGate VM to be stopped. However, updates to virtualization, networking, or kernel components can affect the hypervisor or require a reboot.

A newly installed kernel does not become active until the Ubuntu hypervisor is rebooted.

When maintaining an HA vFSA:

- Review the pending packages before installing them.
- Schedule a maintenance window for kernel, virtualization, or networking updates.
- Plan for a hypervisor reboot when required.
- Update and validate one HA node at a time.
- Confirm that HA and traffic forwarding are healthy before updating the other node.
