---

copyright:
  years: 2024
lastupdated: "2024-01-22"

keywords: reloading, os, upgrading, kvm, ha, standalone

subcollection: vfsa

---

{{site.data.keyword.attribute-definition-list}}

# Rebuilding a High Availability (HA) vFSA cluster
{: #rebuilding-an-ha-cluster}
{: help}
{: support}

You can rebuild a High Availability (HA) {{site.data.keyword.vfsa_full}} cluster by using the information here.
{: shortdesc}

Rebuilding an HA vFSA cluster is a destructive procedure, and should be used only in a few specific scenarios. Consult your IBM Support representative before you begin this procedure.
{: important}

For a successful rebuild of a cluster on an HA vFSA, make sure that you have met the following requirements:

* Do not modify the vFSA configuration during the execution of the Rebuild Cluster operations. Examples include automated software agents attempting to modify one or both vFSA nodes. Configurations changes can corrupt the Rebuild Cluster operation.
* The admin password for the provisioned vFSA gateway must match the admin password that is defined in the vFSA portal. The password in the portal was defined when the gateway was first provisioned, and might not match the current gateway password. If the password was changed after the initial provisioning, then use SSH to connect to the vFSA gateway and change the admin password to match or change the password in the vFSA portal to match. After the passwords match, you can proceed with the Rebuild Cluster operation.

* The host passwords must match the passwords in the vFSA portal. In addition, the host OS must enable root SSH access to the vFSA Private IP before doing a rebuild cluster.

## Rebuilding an HA cluster
{: #rebuild-an-ha-cluster}

To rebuild one of your HA vFSA clusters, follow these steps:

1. From your browser, open the [IBM Cloud console](/login){: external} and log in to your account.
1. Select the Menu icon ![Menu icon](../../icons/icon_hamburger.svg) from the upper left, then click **Classic Infrastructure**.
1. Choose **Network > Gateway Appliances**.
1. Click the server that you want to reload.
1. Click the server name in the Hardware section.
1. Click the **Actions** menu and select **Rebuild Cluster**.
1. Carefully read the warning message. The operation to rebuild a cluster is destructive. If you want to proceed, save your vFSA configuration before you click **Rebuild** to start the process.
