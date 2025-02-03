---

copyright:
  years: 2025
lastupdated: "2025-02-03"

keywords: reloading, os, upgrading, kvm, ha, standalone

subcollection: vfsa

---

{{site.data.keyword.attribute-definition-list}}

# Reloading the OS for {{site.data.keyword.vfsa_short}}
{: #reloading-the-os}
{: help}
{: support}

The OS reload process is used to rebuild a {{site.data.keyword.vfsa_full}} (vFSA) server. This might be necessary in the case of a disk failure, bad configuration of the vFSA node requiring a rebuild, chassis swap, or any other event that requires a rebuild of the server.
{: shortdesc}

The process typically performs the following actions:

* Reload the server host's operating system (OS). This generates a new host OS password.
* Install KVM in the operating system.
* Create a vFSA VM in the KVM.
* Reconfigure the vFSA with the default configuration for IBM Cloud.
* If the node is part of a cluster (HA only), then rejoin the existing cluster and automatically synchronize the configuration, session tables, and so on.

The process can take between 2 and 5 hours to complete. Stand-alone gateways are out of service during this period. For vFSA High Availability (HA) gateways, when you reload the OS on one of your servers, the vFSA fails over to another server in the cluster and continues to process data traffic. After the reload completes, the server rejoins the cluster.

For vFSA, the OS reload process cannot be used to update the vFSA version. You must use the FortiGate Fabric Upgrade tool to change the version. Details on downgrading and upgrading the vFSA version can be found in [Upgrading the vFSA](/docs/vfsa?topic=vfsa-upgrading-the-vfsa).
{: important}

For a successful OS Reload on a vFSA, ensure the following:

* IBM Cloud allows only an OS reload of the vFSA with a certified version. Certified versions can be found in [IBM Cloud Virtual FortiGate Security Appliance supported versions](/docs/vfsa?topic=vfsa-vfsa-versions). When you run an OS reload readiness check, the version running on the vFSA is detected and stored. If this detected version matches a version in the supported version's list, then the OS Reload action can be run. If the node fails the readiness check with a host connectivity issue (blocking the OS reload from proceeding), open a case with IBM Support to override the readiness check to allow the process to proceed.

* Do not modify the vFSA configuration during the OS reload operation. For example, automated software agents attempting to modify one or both vFSA nodes. Configurations changes like these can corrupt the OS reload.

* For HA deployments, the admin password for the provisioned vFSA gateway must match the admin password that is defined in the vFSA portal. The password in the portal was defined when you first provisioned the gateway, and it might not match the current gateway password. If so, use SSH to connect to the vFSA gateway and change the admin password to match. You can then proceed with the OS reload operation.

* The vFSA configuration must allow admin SSH access to the vFSA private IP, before the OS reload request. This is required to rejoin the cluster. After the OS reload completes, if wanted, you can disable SSH access again.

* **Do NOT** perform an OS reload on both servers of the Highly Available gateway at the same time.

   Performing an OS reload on both servers of the HA gateway at the same time destroys the vFSA cluster and causes the gateway to be out of service. If the vFSA cluster is destroyed, you must use the [Rebuild cluster](/docs/vfsa?topic=vfsa-rebuilding-an-ha-cluster) option to reprovision the vFSA and re-create the HA cluster. 
   {: important}

* The IPMI interface for the bare-metal server must be enabled or the following error is issued:

   ```sh
   You cannot toggle the IPMI interface while transactions are running.
   ```

## Performing an OS reload
{: #performing-an-os-reload}

To reload your OS, follow these steps:

1. From your browser, open the [IBM Cloud console](/login){: external} and log in to your account.
1. Run a [Readiness check](/docs/vfsa?topic=vfsa-vfsa-readiness#vfsa-readiness) for “OS Reload” and address any errors that are found.
1. Select the Menu icon ![Menu icon](../../icons/icon_hamburger.svg) from the upper left, then click **Classic Infrastructure**.
1. Choose **Network > Gateway Appliances**.
1. Click the server that you want to reload.
1. Click the server name in the Hardware section.
1. Select **OS Reload** from the **Actions** menu on the upper right of the page.
1. In the **OS Reload** screen, click **Edit** for the Category that requires an update. Select **Juniper** as the Vendor, and the OS version you want to reload.
1. Click the **Reload Above Configuration** button to proceed to the **Review** window. Click **Cancel** to cancel the changes to the device and exit the screen.
1. Verify that all details in the **New Configuration** section are correct. Click **Next** to advance to the Confirm window.
1. Click the **Confirm OS Reload** button to confirm and initiate the OS Reload. Or, click **Cancel** to cancel the action.

## vFSA version mismatches
{: #vfsa-version-mismatches}

A {{site.data.keyword.vfsa_full}} cluster must have the same vFSA version on each node in order to fully support the High Availability feature. If a cluster has mismatched vFSA versions, then it might be necessary to contact IBM Support to force rebuild the entire cluster to a supported version.

A version mismatch applies to both minor release mismatches and major release mismatches.
{: note}
