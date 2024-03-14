---

copyright:
  years: 2024
lastupdated: "2024-01-22"

keywords: understanding, default, configuration, standalone, ha

subcollection: vfsa

---

{{site.data.keyword.attribute-definition-list}}

# Importing and and exporting a vFSA configuration
{: #importing-exporting-vfsa-configuration}

The {{site.data.keyword.vfsa_full}} OS Reload for Highly Available (HA) configurations preserves the original configuration of the vFSA throughout the entire process. However, it is still strongly recommended you export and backup your vFSA configuration settings before an OS reload. An OS reload of a standalone vFSA or a rebuild cluster action will not preserve the configuration. In addition, backup and restoration of the configuration will be required to restore your current configuration.
{: shortdesc}

After the OS reload process completes for Stand Alone servers or rebuild cluster for all servers, you should import the original configuration you saved if you want to restore it.

## Considerations
{: #considerations}

* Use the FortiGate Fabric Upgrade tool to downgrade and upgrade the vFSA version. This process automatically backs up and restores the configuration with no manual intervention needed. Details on the downgrade and upgrade can be found at [Upgrading the vFSA](/docs/vfsa?topic=vfsa-upgrading-the-vfsa).

## vFSA configuration backup and restore
{: #export-the-whole-vfsa-configuration}

Follow the Fortinet documentation using either the GUI or the CLI. This process for version 7.4.1 is described [here](https://docs.fortinet.com/document/FortiGate/7.4.1/administration-guide/702257/configuration-backups){: external}.
