---

copyright:
  years: 2026
lastupdated: "2026-07-28"

keywords: version, base version, release notes, juniper

subcollection: vfsa

---

{{site.data.keyword.attribute-definition-list}}

# IBM Cloud Virtual FortiGate Security Appliance supported versions
{: #vfsa-versions}

This document lists the supported vFSA versions that you can currently provision and are available at time of order. To help keep your system secure, you must update to the latest available version from Fortinet after provisioning. IBM Cloud Support and Fortinet support the latest FortiGate vFSA software versions, even if these versions are not available during the initial provisioning process.
{: shortdesc}

You can click on the **Version information** link for each entry to get more details about that version.

| Base version | Release version | Hypervisor | Release date | Version information |
| --- | --- | --- | --- | --- |
| 7.4.11 | v7.4.11 build2795 | Ubuntu 24.04 with KVM | June 2, 2026 | [More information](https://docs.fortinet.com/document/fortigate/7.4.11/fortios-release-notes/760203/introduction-and-supported-models){: external} |
| 7.6.4 | v7.6.4 build3596 | Ubuntu 22.04 with KVM | Dec 3, 2025 | [More information](https://docs.fortinet.com/document/fortigate/7.6.4/fortios-release-notes/760203/introduction-and-supported-models){: external} |
| 7.4.8 | v7.4.8 build2795 | Ubuntu 22.04 with KVM | Sept 8, 2025 | [More information](https://docs.fortinet.com/document/fortigate/7.4.8/fortios-release-notes/760203/introduction-and-supported-models){: external} |
| 7.6.2 | v7.6.2 build3462 | Ubuntu 22.04 with KVM | March 12, 2025 | [More information](https://docs.fortinet.com/document/fortigate/7.6.2/fortios-release-notes/760203){: external} |
| 7.6.1 | v7.6.1 build3457 | Ubuntu 22.04 with KVM | Feb 3, 2025 | [More information](https://docs.fortinet.com/document/fortigate/7.6.1/fortios-release-notes/760203/introduction-and-supported-models){: external} |
| 7.6.0 | v7.6.0 build3401 | Ubuntu 20.04 with KVM | Sept 5, 2024 | [More information](https://docs.fortinet.com/document/fortigate/7.6.0/fortios-release-notes/760203/introduction-and-supported-models){: external} |
| 7.4.4 | v7.4.4 build2662 | Ubuntu 20.04 with KVM | June 13, 2024 | [More information](https://docs.fortinet.com/document/fortigate/7.4.4/fortios-release-notes/760203/introduction-and-supported-models){: external} |
| 7.4.3 | v7.4.3 build2573 | Ubuntu 20.04 with KVM | March 18, 2024 | [More information](https://docs.fortinet.com/document/FortiGate/7.4.3/fortios-release-notes/760203){: external} |
{: caption="IBM Cloud Virtual FortiGate Security Appliance supported versions" caption-side="bottom"}

The supported versions listed here are the versions that IBM has qualified for provision and that can be provisioned through our automation. We don't qualify every minor version of this software. As a result, if you need to perform an OS reload, the vFSA will be provisioned with one of the versions on this list. After the reload completes, you can then upgrade the vFSA to any Fortigate supported version using the [vFSA upgrade methods](/docs/vfsa?topic=vfsa-upgrading-the-vfsa).

If IBM hasn't qualified a particular version for our automation, then it can't be assigned during provisioning of a new vFSA. However, you can and must upgrade to that version after provisioning (as decribed in the previous section), as long as it is a version later than `7.4.1`. Versions earlier than `7.4.1` are not supported.
