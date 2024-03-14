---

copyright:
  years: 2024
lastupdated: "2024-01-22"

keywords: checking, readiness

subcollection: vfsa

---

{{site.data.keyword.attribute-definition-list}}

# Readiness checks
{: #vfsa-readiness}

A readiness check verifies the ability of your {{site.data.keyword.vfsa_full}} to perform certain gateway actions, including:

* OS reloads
* License upgrades

Version upgrades are not supported with the Virtual FortiGate Security Appliance. You must use the FortiGate Fabric Upgrade tool to change the version. Details on downgrading and upgrading the vFSA version can be found in [Upgrading the vFSA](/docs/vfsa?topic=vfsa-upgrading-the-vfsa).
{: important}

After you run the readiness check, errors alert you to any necessary actions that you should take before beginning one of these actions, or inform you that you're ready to proceed.

To run a readiness check, follow these steps:

1. From your browser, open the [IBM Cloud console](/login){: external} and log into your account.
2. Select the Menu icon ![Menu icon](../../icons/icon_hamburger.svg) from the upper left, then click **Classic Infrastructure**.
3. Choose **Network > Gateway Appliances**.
4. Click the name of the vFSA that you want to run a readiness check on.
5. Find the Readiness Check module on the vFSA details page.
6. Click the **Run check** button.
7. Select one of the three actions that you want to check for readiness, then select **Run check**.

   The details page for your vFSA displays again, as do the test results in the readiness check module.

   Ensure the status for any action you want to perform is Ready before beginning that action.
   {: important}

The following section provides details on the various readiness status conditions your check might return.

## Readiness status
{: #readiness-status}

There are seven unique status conditions for the readiness check that you might encounter.

### Unchecked
{: #status-unchecked}

A readiness check has not yet been run for this action.

### Expired
{: #status-expired}

The readiness check has not run recently enough to reflect accurate results. Run a new check to see the current status.

### Ready
{: #status-ready}

Your vFSA is ready to perform the given action.

### Not Ready
{: #status-not-ready}

Your vFSA is not ready to perform the action in question. This could occur because of several reasons. Either a readiness check error occurred, or the readiness check did not complete fast enough, and timed out.

Error messages for the issues found during the readiness check display next to the module. Click the error codes to get more information on each error. Alternatively, you can find information about each error in [Understanding readiness errors](/docs/vfsa?topic=vfsa-readiness-errors).

### Running
{: #status-running}

The readiness check is currently running on your vFSA and currently, has not encountered any errors.

### Incomplete
{: #status-incomplete}

The first member of the gateway's highly available setup failed the readiness check. As a result, the gateway could not complete the readiness check.

### Unsupported
{: #status-unsupported}

The action you are attempting to check is not supported for this gateway.

### Current
{: #status-current}

The action you are attempting to check does not need to be performed, as the gateway already has the latest version available.
