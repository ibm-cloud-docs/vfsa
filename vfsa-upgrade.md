---

copyright:
  years: 2024
lastupdated: "2024-01-22"

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
