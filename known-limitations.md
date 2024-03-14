---

copyright:
  years: 2024
lastupdated: "2024-01-22"

keywords:

subcollection: vfsa

---

{{site.data.keyword.attribute-definition-list}}

# Known limitations for {{site.data.keyword.vfsa_full}}
{: #known-limitations-for-ibm-cloud-vfsa}

There are some limitations to be aware of when using {{site.data.keyword.vfsa_full}}.
{: shortdesc}

* There is no support to upgrade from Stand-alone to High Availability mode.

* There is no support to downgrade from High Availability to Stand-alone mode.

* There is no support to upgrade from 1 Gbps to 10 Gbps or downgrade from 10 Gbps to 1 Gbps.

* Only IBM Cloud certified versions of vFSA are supported for actions like OS Reload, License Update, Rebuild Cluster. The list of supported versions can be found [here](/docs/vfsa?topic=vfsa-vfsa-versions).

* Only the latest IBM Cloud certified vFSA releases are available for new orders and upgrades. Requests for older IBM Cloud certified vFSA releases should be made through an IBM Support case. Limitations for upgrading and downgrading can be found in [Upgrading the vFSA](/docs/vfsa?topic=vfsa-upgrading-the-vfsa).

* vFSA license validation requires a public internet connection to the Fortinet Cloud for license entitlement.

* There is no access to FortiCloud or FortiGate Cloud logging and management because licensing is registered to IBM's Fortinet Account.
