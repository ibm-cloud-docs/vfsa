---

copyright:
  years: 2024
lastupdated: "2024-11-04"

keywords: version, base version, release notes, juniper

subcollection: vfsa

---

{{site.data.keyword.attribute-definition-list}}

# IBM Cloud Virtual FortiGate Security Appliance supported versions
{: #vfsa-versions}

This document lists the currently supported vFSA versions.
{: shortdesc}

You can click on the **Version information** link for each entry to get more details about that version.

| Base version | Release version | Hypervisor | Release date | Version information |
| --- | --- | --- | --- | --- |
| 7.6.0 | v7.6.0 build3401 | Ubuntu 20.04 with KVM | Sept 5, 2024 | [More information](https://docs.fortinet.com/document/fortigate/7.6.0/fortios-release-notes/760203/introduction-and-supported-models){: external} |
| 7.4.4 | v7.4.4 build2662 | Ubuntu 20.04 with KVM | June 13, 2024 | [More information](https://docs.fortinet.com/document/fortigate/7.4.4/fortios-release-notes/760203/introduction-and-supported-models){: external} |
| 7.4.3 | v7.4.3 build2573 | Ubuntu 20.04 with KVM | March 18, 2024 | [More information](https://docs.fortinet.com/document/FortiGate/7.4.3/fortios-release-notes/760203){: external} |
{: caption="IBM Cloud Virtual FortiGate Security Appliance supported versions" caption-side="bottom"}

The supported versions listed here are the versions that IBM has qualified for use and that can be provisioned through our automation. We don't qualify every minor version of this software. As a result, if you need to perform an OS reload, the vFSA will be provisioned with one of the versions on this list. After the reload completes, you can then upgrade the vFSA to any Fortigate supported version using the [vFSA upgrade methods](/docs/vfsa?topic=vfsa-upgrading-the-vfsa).

If IBM has not qualified a particular version for our automation, then it cannot be assigned during provisioning of a new vFSA. However, you can upgrade to that version after provisioning (as decribed above), as long as it is a version later than 7.4.1. Versions earlier than 7.4.1 are not supported.
