---

copyright:
  years: 2026
lastupdated: "2026-11-10"

keywords: vulnerability, vulnerability management, patching, PSIRT, security advisory, FortiOS update, firmware update, Ubuntu security updates, shared responsibilities

subcollection: vfsa

---

{{site.data.keyword.attribute-definition-list}}

# Managing firmware updates and vulnerability patches
{: #managing-vulnerabilities}

[Begin change - entire topic is new]{: tag-green} Keeping abreast of Fortinet and Ubuntu security advisories and applying updates in a timely manner is your responsibility. {{site.data.keyword.cloud}} provisions the {{site.data.keyword.vfsa_full}}, provides support, and sends notifications about critical Fortinet security issues, but does not apply FortiOS, Ubuntu, or other software updates to your vFSA on your behalf.
{: shortdesc}

The {{site.data.keyword.vfsa_full}} is not a managed service. After provisioning, you (or your managed services provider) are responsible for vulnerability management of both software components of the vFSA:

- The FortiGate VM running FortiOS
- The Ubuntu KVM hypervisor that hosts the FortiGate VM

For a full description of client and IBM responsibilities, see [Roles and responsibilities for IBM Cloud gateways and firewalls](/docs/vfsa?topic=vfsa-ga-raci).

## Responsibilities for vulnerability management
{: #vfsa-vuln-responsibilities}

| Activity | Client | IBM |
| -------- | ------ | --- |
| Monitor Fortinet PSIRT advisories and Ubuntu security notices | R, A | C, I |
| Send notifications about critical Fortinet security issues | I | R, A |
| Assess whether an advisory affects your deployment | R, A | C, I |
| Plan, schedule, and apply FortiOS firmware updates and security patches | R, A | C, I |
| Plan, schedule, and apply Ubuntu hypervisor package, kernel, and security updates | R, A | C, I |
| Back up the FortiGate configuration before patching | R, A | C, I |
| Verify the vFSA after patching | R, A | C, I |
| Maintain the underlying IBM Cloud infrastructure outside of your environment | C, I | R, A |
{: caption="Vulnerability management responsibilities" caption-side="top"}

For a description of R, A, C, and I, see [Roles and responsibilities for IBM Cloud gateways and firewalls](/docs/vfsa?topic=vfsa-ga-raci#ga-raci-overview).

## Step 1: Staying informed about security issues
{: #vfsa-vuln-subscribe}

{{site.data.keyword.cloud}} sends notifications about critical Fortinet security issues. These notifications don't replace your own monitoring and don't cover Ubuntu. Use the following sources to stay informed when new security advisories and firmware releases are published.

- **Fortinet PSIRT portal** - Fortinet's Product Security Incident Response Team (PSIRT) publishes advisories for all known vulnerabilities that affect FortiGate products, including severity ratings, affected versions, and recommended remediation actions. Review the [Fortinet PSIRT portal](https://www.fortiguard.com/psirt){: external} regularly.
- **Fortinet RSS feeds** - Subscribe to Fortinet RSS feeds for security advisories, firmware releases, and product announcements. For more information, see [Manage Your Subscriptions to Fortinet](https://www.fortinet.com/rss-feeds){: external}.
- **Ubuntu Security Notices** - Review the [Ubuntu Security Notices](https://ubuntu.com/security/notices){: external} regularly for vulnerabilities that affect the hypervisor, such as the Linux kernel, `libvirt`, and `qemu`.

Include these sources in your organization's vulnerability management process.

## Step 2: Assessing the impact on your deployment
{: #vfsa-vuln-assess}

When a new advisory is published, determine whether your deployment is affected before you take action.

1. Identify the FortiOS version that is running on your vFSA. In the FortiGate web interface, go to **Dashboard > Status** and note the firmware version that is displayed under **System Information**.
2. Compare your running version against the affected versions that are listed in the advisory.
3. Review the advisory's CVSS severity score and any available mitigations or workarounds.
4. For Ubuntu advisories, run `apt list --upgradable` on the hypervisor to see whether a fix is available.

## Step 3: Backing up your configuration before patching
{: #vfsa-vuln-backup}

Before you apply any update, back up your FortiGate configuration. This protects you if the update needs to be rolled back.

For instructions, see [Importing and exporting a vFSA configuration](/docs/vfsa?topic=vfsa-importing-exporting-vfsa-configuration).

## Step 4: Applying the update
{: #vfsa-vuln-apply}

FortiOS and Ubuntu are maintained differently.

- **FortiOS** - Use the FortiGate firmware upgrade tools, such as **Fabric Upgrade**, in the FortiGate web interface. Do not use an IBM Cloud OS reload only to upgrade FortiOS. For the procedure, version requirements, and HA considerations, see [Updating FortiOS](/docs/vfsa?topic=vfsa-upgrading-the-vfsa#updating-fortios).
- **Ubuntu hypervisor** - Apply routine package, kernel, and security updates by using the standard Ubuntu package-management process. Use an IBM Cloud OS reload to move to another major Ubuntu LTS release. For the procedure and HA considerations, see [Ubuntu hypervisor maintenance](/docs/vfsa?topic=vfsa-upgrading-the-vfsa#ubuntu-hypervisor-maintenance) and [Updating the Ubuntu hypervisor](/docs/vfsa?topic=vfsa-upgrading-the-vfsa#updating-the-ubuntu-hypervisor).

Schedule a maintenance window because the FortiGate VM restarts during a FortiOS upgrade, and a hypervisor reboot is required to activate a new kernel. For an HA vFSA, update and validate one node at a time.

A vFSA can and must run a FortiOS release that is newer than the versions that are available for IBM Cloud provisioning or OS reload operations. Before an OS reload or license update, the vFSA must be running a qualified FortiOS version. For details, see [Updating FortiOS](/docs/vfsa?topic=vfsa-upgrading-the-vfsa#updating-fortios).
{: note}

## Step 5: Verifying and confirming
{: #vfsa-vuln-verify}

After the update is complete:

1. Log in to the FortiGate web interface.
2. Confirm the firmware version under **Dashboard > Status > System Information**. For the hypervisor, confirm the package or kernel version.
3. Verify network connectivity, HA status, and that firewall policies are operating as expected.
4. Review the advisory to confirm that the installed version is listed as a remediated release.

## Related links
{: #managing-vulnerabilities-related-links}

- [Roles and responsibilities for IBM Cloud gateways and firewalls](/docs/vfsa?topic=vfsa-ga-raci)
- [Upgrading the vFSA](/docs/vfsa?topic=vfsa-upgrading-the-vfsa)
- [Importing and exporting a vFSA configuration](/docs/vfsa?topic=vfsa-importing-exporting-vfsa-configuration)
- [Securing the host operating system](/docs/vfsa?topic=vfsa-securing-host-operating-system)
- [Fortinet PSIRT portal](https://www.fortiguard.com/psirt){: external}
- [Ubuntu Security Notices](https://ubuntu.com/security/notices){: external}

[End change - entire topic is new]{: tag-green}
