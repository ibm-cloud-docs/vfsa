---

copyright:
  years: 2024
lastupdated: "2024-01-22"

keywords: readiness errors

subcollection: vfsa

---

{{site.data.keyword.attribute-definition-list}}

# Correcting readiness errors and warnings
{: #correcting-readiness-errors}

Readiness errors and warnings can inhibit your ability to successfully complete a readiness check. This topic provides information on correcting different types of errors and warnings.
{: shortdesc}

## Correcting connectivity errors
{: #connnectivity-errors}

There are two categories of connectivity errors that you might encounter when conducting readiness checks:

* Host (Ubuntu) SSH connectivity errors
* Gateway (vFSA) SSH connectivity errors

Many of these errors result from the fact that the gateway actions being checked require root SSH access to the private IP address for either the host (Ubuntu) OS, or the gateway (vFSA). If an SSH connectivity check fails, then the action cannot proceed.

   For more information about establishing an SSH session, see [Accessing the device using SSH](/docs/vfsa?topic=vfsa-performing-ibm-cloud-juniper-vfsa-basics#accessing-the-device-using-ssh). Note that for step 3, the example that is given is with the `admin` user. For a readiness check, substitute the `root` user for both the vFSA and the Hardware (host). Also, make sure that you use your private IP with this procedure, not your public IP.
   {: note}

To validate connectivity, open an SSH session to either the Ubuntu host's or vFSA's private IP using the root credentials that are listed in the **Hardware** section (for an Ubuntu host) or the **vFSA** section (for the gateway) of the [Gateway Appliance Details](/docs/gateway-appliance?topic=gateway-appliance-viewing-gateway-appliance-details) page. Ensure that the SSH session can be established.

If the session cannot be established, check the following potential issues.

For host (Ubuntu) SSH connectivity errors:

* Is the Ubuntu firewall blocking SSH access to the private IP? The firewall rules must allow SSH access to the private `10.0.0.0/8` subnet. 
* Is the root password listed on the Gateway Appliance Details page the correct password for the root user?
    If not, click the device link in the **Hardware** section and navigate to **Passwords**. Select **Actions > Edit credentials** and change the password to match the actual root password on the Ubuntu host.
* Is the root login disabled for the SSH server?
* Is the SSH server disabled or stopped?
* Is the root user account disabled on the Ubuntu host?

For gateway (vFSA) SSH connectivity errors:

* Is the vFSA firewall blocking SSH access to the private IP? The firewall rules must allow SSH access to the private `10.0.0.0/8` subnet. 
* Is the admin user password listed on the Gateway Appliance Details page the correct password for the admin user? If not, click the **Edit** icon ![Edit icon](../icons/edit-tagging.svg) next to the admin password and change the password to match the actual admin password for the vFSA.
* Is the admin user account disabled for SSH access to the vFSA?

## Correcting error 1148
{: #correcting-1148}

An unstable or unformed HA cluster can occur if the vFSA version running on the Gateway is older than version 7.4.1. Use the Fortinet Fabric Upgrade tool to move to a version of at least 7.4.1. Details on downgrading and upgrading the vFSA version can be found in [Upgrading the vFSA](/docs/vfsa?topic=vfsa-upgrading-the-vfsa). If the upgrade fails or the cluster is not formed after the upgrade you might need to rebuild the cluster. 
